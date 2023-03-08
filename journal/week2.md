# Week 2 — Distributed Tracing

In week 2 of the AWS Cloud Project Bootcamp, I learned how to instrument a Flask backend application to use Open Telemetry (OTEL) with Honeycomb.io as the provider. I also learned how to run queries to explore traces within Honeycomb.io. Additionally, I learned how to instrument AWS X-Ray into the backend Flask application and configure and provision the X-Ray daemon within Docker Compose to send data back to the X-Ray API. I also learned how to observe X-Ray traces within the AWS Console.

I also integrated Rollbar for Error Logging and triggered an error to observe it with Rollbar. Finally, I learned how to install WatchTower and write a custom logger to send application log data to a CloudWatch Log group. Overall, these skills are important for monitoring and debugging applications running on AWS, ensuring that issues can be quickly identified and resolved.


Below are the Technical tasks required to complete the requirements for week2 tasks.

- Instrument our backend flask application to use Open Telemetry (OTEL) with Honeycomb.io as the provider.

  first I created an account on https://honeycomb.io/ then added the code below to the requirements.txt to install dependencies when gitpod is opened.
  
     ```
          opentelemetry-api 
          opentelemetry-sdk 
          opentelemetry-exporter-otlp-proto-http 
          opentelemetry-instrumentation-flask 
          opentelemetry-instrumentation-requests
  
     ```
     
     Then Run ``` pip3 -r requirements.txt ``` add code below to app.py
     
     ```
                # HoneyComb ---------
        from opentelemetry import trace
        from opentelemetry.instrumentation.flask import FlaskInstrumentor
        from opentelemetry.instrumentation.requests import RequestsInstrumentor
        from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
        from opentelemetry.sdk.trace import TracerProvider
        from opentelemetry.sdk.trace.export import BatchSpanProcessor
        from opentelemetry.sdk.trace.export import ConsoleSpanExporter, SimpleSpanProcessor

        # Initialize tracing and an exporter that can send data to Honeycomb
        provider = TracerProvider()
        processor = BatchSpanProcessor(OTLPSpanExporter())
        provider.add_span_processor(processor)

        # Show this in the logs within the backend-flask app (STDOUT)
        simple_processor = SimpleSpanProcessor(ConsoleSpanExporter())
        provider.add_span_processor(simple_processor)
        trace.set_tracer_provider(provider)
        tracer = trace.get_tracer(__name__)

     ```

     ```
        # Initialize automatic instrumentation with Flask
        app = Flask(__name__)   ## Already exists
        FlaskInstrumentor().instrument_app(app)
        RequestsInstrumentor().instrument()
     
     ```
     
     Update the docker compose file with the environment variables  below for the backend flask
     
     ```
          OTEL_SERVICE_NAME: 'backend-flask'
          OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
          OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"

     ```
     
     Export the environment variable honeycomb API key and the service name and save to gitpod
     
     ```
          export HONEYCOMB_API_KEY=""
          export HONEYCOMB_SERVICE_NAME="cruddur"
          gp env HONEYCOMB_API_KEY=""
          gp env HONEYCOMB_SERVICE_NAME="cruddur"
     
     ```

    
    I ran my container using ```docker compose up``` then got some data in the homeycomb home page 
    
    <img width="1440" alt="Screen Shot 2023-03-08 at 7 27 40 PM" src="https://user-images.githubusercontent.com/63635704/223786278-bc186e76-6813-4f5b-b317-0e6a827cde51.png">
    
    I open port 4567 & 3000 the open both url then refreshed the honeycomb ui to get some data
    
    
    <img width="1426" alt="Screen Shot 2023-03-08 at 7 21 15 PM" src="https://user-images.githubusercontent.com/63635704/223787100-06f6a697-b841-4cfe-9bc3-72841bd0377f.png">
    
 
- Run queries to explore traces within Honeycomb.io

    Ran new query to visualize concurrency in honeycomb
    
   <img width="1427" alt="Screen Shot 2023-03-08 at 7 34 35 PM" src="https://user-images.githubusercontent.com/63635704/223787870-fdcb73aa-eaa0-4ee2-baca-8429a1888d48.png">

   add opentelemetry in home_activities.py to acquire tracer info
   
   ```
      from opentelemetry import trace

      tracer = trace.get_tracer("home.activities")
   
   ```
   
   created spans with code below
   
   ``` 
      with tracer.start_as_current_span("home-activites-mock-data"):
      span = trace.get_current_span()
   
   ```
   
   after running the container, I got data in Honeycomb with 2 spans
   
   
<img width="1259" alt="Screen Shot 2023-03-08 at 7 46 34 PM" src="https://user-images.githubusercontent.com/63635704/223790408-27adcc8d-a485-489c-baba-2ea14dab1e20.png">

<img width="1395" alt="Screen Shot 2023-03-08 at 7 47 01 PM" src="https://user-images.githubusercontent.com/63635704/223790421-c3721ef0-8af4-4629-b5da-a8b161291556.png">
   


- Instrument AWS X-Ray into backend flask application

   first, export the default aws region and save in gitpod config
   
   ```
       export AWS_REGION="us-east-1"
       gp env AWS_REGION="us-east-1"
   
   ```
   
   Add to the ```requiremnts.txt```
   
   ```
      aws-xray-sdk
   
   ```
   
   Then install python dependencies
   
   ``` 
      pip install -r requirements.txt 
   ```
   
   Add to app.py 
   
   ```
      from aws_xray_sdk.core import xray_recorder
      from aws_xray_sdk.ext.flask.middleware import XRayMiddleware

      ......

      xray_url = os.getenv("AWS_XRAY_URL")
      xray_recorder.configure(service='backend-flask', dynamic_naming=xray_url)

      .....

      app = Flask(__name__)  # already exists
      # X-Ray ---------
      XRayMiddleware(app, xray_recorder)
   
   ```
   
   next we create a file xray.json inside aws/json with code below
   
   
   ```
        {
            "SamplingRule": {
                "RuleName": "Cruddur",
                "ResourceARN": "*",
                "Priority": 9000,
                "FixedRate": 0.1,
                "ReservoirSize": 5,
                "ServiceName": "backend-flask",
                "ServiceType": "*",
                "Host": "*",
                "HTTPMethod": "*",
                "URLPath": "*",
                "Version": 1
            }
          }
      
   ```
   
     create an x-ray group in AWS
     
     ```
        aws xray create-group \
           --group-name "Cruddur" \
           --filter-expression "service(\"backend-flask\")"
     
     ```
     
     create sampling rule
     
     ```
        aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json
     
     ```
   <img width="1138" alt="Screen Shot 2023-03-08 at 8 17 59 PM" src="https://user-images.githubusercontent.com/63635704/223797788-9cc3e87e-d0a6-412d-9c70-490c7be83ce3.png">
   


- Configure and provision X-Ray daemon within docker-compose and send data back to X-Ray API
Add Deamon to Docker Compose

  ```
      xray-daemon:
        image: "amazon/aws-xray-daemon"
        environment:
          AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
          AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
          AWS_REGION: "us-east-1"
        command:
          - "xray -o -b xray-daemon:2000"
        ports:
          - 2000:2000/udp
  ```
  
  next we will add to env var to our backend-flask in ```docker-compose.yml```
  
  ```
       AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
       AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
  ```     

- Observe X-Ray traces within the AWS Console

     I ran my container and open the url which had some traces and i viewed them on the AWS console
     
     <img width="1144" alt="Screen Shot 2023-03-08 at 8 29 30 PM" src="https://user-images.githubusercontent.com/63635704/223804047-1f0f7e37-5736-4c29-ab42-a7f71f82d6b0.png">

<img width="1186" alt="Screen Shot 2023-03-08 at 8 30 22 PM" src="https://user-images.githubusercontent.com/63635704/223804064-1d2e17a5-d42c-4689-8bfc-3f6ede7aa31e.png">

I updated my code to view traces with subsegments()mock-data) in ``` user_activities.py``` file

      ``` from datetime import datetime, timedelta, timezone
    from aws_xray_sdk.core import xray_recorder
    class UserActivities:
      def run(user_handle):
        try:
          model = {
            'errors': None,
            'data': None
          }

          now = datetime.now(timezone.utc).astimezone()

          if user_handle == None or len(user_handle) < 1:
            model['errors'] = ['blank_user_handle']
          else:
            now = datetime.now()
            results = [{
              'uuid': '248959df-3079-4947-b847-9e0892d1bab4',
              'handle':  'Andrew Brown',
              'message': 'Cloud is fun!',
              'created_at': (now - timedelta(days=1)).isoformat(),
              'expires_at': (now + timedelta(days=31)).isoformat()
            }]
            model['data'] = results

          subsegment = xray_recorder.begin_subsegment('mock-data')
          # xray ---
          dict = {
            "now": now.isoformat(),
            "results-size": len(model['data'])
          }
          subsegment.put_metadata('key', dict, 'namespace')
          xray_recorder.end_subsegment()
        finally:  
        #  # Close the segment
          xray_recorder.end_subsegment()
        return model
    
    ```
    
   Visual trace epresentation of the subsegments i configured with x-ray in my code in management console
   
 <img width="1389" alt="Screen Shot 2023-03-08 at 9 01 37 PM" src="https://user-images.githubusercontent.com/63635704/223811282-1afc0a6d-a20e-461f-b9fb-dda693e8ecfb.png">



- Integrate Rollbar for Error Logging
    
    first create a new project in rollbar called Cruddur 
    
    Next add to the ```requirements.txt``` file dependencies 
    
    ``` 
        blinker
        rollbar
    
    ```
    
    install dependencies 
    
    ```
        pip install -r requirements.txt
    
    ```
  
    copy the access token on rollbar then set as env var and save on gitpod . ALso update in backend-flask on ```docker-compose.yml```
    
    ```
       export ROLLBAR_ACCESS_TOKEN="************"
       gp env ROLLBAR_ACCESS_TOKEN="************"
    
    ```
    
    
    Import libraries for Rollbar in ```app.py```
    
    ```
        import os
        import rollbar
        import rollbar.contrib.flask
        from flask import got_request_exception
        
    ```
    



- Trigger an error an observe an error with Rollbar
   
   Add code and endpoint to test Rollbar to ```app.py file```
   
   ```
      rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
      @app.before_first_request
      def init_rollbar():
          """init rollbar module"""
          rollbar.init(
              # access token
              rollbar_access_token,
              # environment name
              'production',
              # server root directory, makes tracebacks prettier
              root=os.path.dirname(os.path.realpath(__file__)),
              # flask already sets up logging
              allow_logging_basic_config=False)

          # send exceptions from `app` to rollbar, using flask's signal system.
          got_request_exception.connect(rollbar.contrib.flask.report_exception, app)
   
   ```
   
   ```
   @app.route('/rollbar/test')
   def rollbar_test():
       rollbar.report_message('Hello World!', 'warning')
       return "Hello World!"
   
   ```

    Goto to Rollbar UI to check items to confirm success 
    
    <img width="1392" alt="Screen Shot 2023-03-08 at 12 38 13 PM" src="https://user-images.githubusercontent.com/63635704/223828130-fa0ea38f-42be-4af7-a4e8-52171019b4a4.png">
    
    


- Install WatchTower and write a custom logger to send application log data to - CloudWatch Log group

    first step add to the ```requirements.txt``` 
    
    ```
        watchtower
        
    ```
    
    then run 
    
    ``` 
       pip3 install -r requirements.txt 
    ```

    import the libraries in app.py 
    
    ```
        import watchtower
        import logging
        from time import strftime
    ```    
    
    we'll configure logger to use cloudwatch then test log 
    
    ```
        #Configuring Logger to Use CloudWatch
        LOGGER = logging.getLogger(__name__)
        LOGGER.setLevel(logging.DEBUG)
        console_handler = logging.StreamHandler()
        cw_handler = watchtower.CloudWatchLogHandler(log_group='cruddur')
        LOGGER.addHandler(console_handler)
        LOGGER.addHandler(cw_handler)
        LOGGER.info("test log")

   ```
   
   set env var for backend-flask in ```docker-compose.yml```
   
   ```
      AWS_DEFAULT_REGION: "${AWS_DEFAULT_REGION}"
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
  ```
  
  after running ```docker-compose up``` i refreshed my home page to ge some traces in CloudWatch UI
   

<img width="1118" alt="Screen Shot 2023-03-08 at 9 24 41 PM" src="https://user-images.githubusercontent.com/63635704/223823577-4731b65f-f341-4480-b624-df17f7e0ffd0.png">

