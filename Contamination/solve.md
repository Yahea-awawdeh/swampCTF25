# Challenge description

I have created a safe reverse proxy that only forwards requests to retrieve debug information from the backend. What could go wrong?

# Solve
In this challenge there is a sorce and there is two main code

# backend.py
```
from flask import Flask, jsonify, request
import os
import logging

app = Flask(__name__)

app.config['DEBUG'] = os.getenv('DEBUG', 'False')
app.config['LOG_LEVEL'] = os.getenv('LOG_LEVEL', 'warning')


@app.route('/api', methods=['POST'])
def api():
    param = request.args.get('action')
    app.logger.info(f"Received param: {param}")

    if param == 'getFlag':
        try:
            data = request.get_json()
            app.logger.info(f"Received JSON data: {data}")
            return jsonify(message="Prased JSON successfully")
        except Exception as e:
            app.logger.error(f"Error parsing JSON: {e}")
            debug_data = {
                'headers': dict(request.headers),
                'method': request.method,
                'url': request.url,
                'env_vars': {key: value for key, value in os.environ.items()}
            }
            return jsonify(message="Something broke!!", debug_data=debug_data)

    if param == 'getInfo':
        debug_status = app.config['DEBUG']
        log_level = app.config['LOG_LEVEL']
        return jsonify(message="Info retrieved successfully!", debug=debug_status, log_level=log_level)

    return jsonify(message="Invalid action parameter!", param=param)


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

# server.rb:
```
require 'sinatra'
require 'rack/proxy'
require 'json'

class ReverseProxy < Rack::Proxy
  def perform_request(env)
    request = Rack::Request.new(env)

    # Only allow requests to the /api?action=getInfo endpoint
    if request.params['action'] == 'getInfo'
      env['HTTP_HOST'] = 'backend:5000'
      env['PATH_INFO'] = '/api'
      env['QUERY_STRING'] = request.query_string
      body = request.body.read
      env['rack.input'] = StringIO.new(body)
      
      begin
        json_data = JSON.parse(body)
        puts "Received valid JSON data: #{json_data}"
        super(env)
      rescue JSON::ParserError => e
        puts "Error parsing JSON: #{e.message}"
        return [200, { 'Content-Type' => 'application/json' }, [{ message: "Error parsing JSON", error: e.message }.to_json]]
      end
    else
      [200, { 'Content-Type' => 'text/plain' }, ["Unauthorized"]]
    end
  end
end

use ReverseProxy

set :bind, '0.0.0.0'
set :port, 8080
puts "Server is listening on port 8080..."
```
# Analyze:

Reverse Proxy (Ruby/Sinatra):
<ul>
<li>Only forwards requests with action=getInfo.</li>

<li>Validates JSON syntax but ignores Content-Type</li>
</ul>

Backend Server (Python/Flask):
<ul>
<li>Endpoint /api accepts POST requests.</li>

<li>Action getFlag: Parses JSON and leaks os.environ on error.</li>

<li>Action getInfo: Returns basic debug info.</li>
</ul>

# Vulnerability
<ul>
  
<li>Parameter Pollution: The proxy and backend parse action from different sources. </li>
  
<li>Insecure Error Handling: Backend returns os.environ on JSON parsing errors. </li>
  
</ul>
  
# Exploit
```
URL/api?action=getFlag&action=getInfo

{"anything":"\'\'"}
```
![image](https://github.com/user-attachments/assets/fb55a150-0f73-4589-8374-27602e2b2a5a)

# FLAG:swampCTF{1nt3r0p3r4b1l1ty_p4r4m_p0llut10n_x7q9z3882e}
