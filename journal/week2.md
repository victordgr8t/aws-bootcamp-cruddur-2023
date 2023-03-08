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
          export HONEYCOMB_API_KEY="${HONEYCOMB_API_KEY}"
          export HONEYCOMB_SERVICE_NAME="cruddur"
          gp env HONEYCOMB_API_KEY=""
          gp env HONEYCOMB_SERVICE_NAME="${HONEYCOMB_API_KEY}"
     
     ```

    
    I ran my container using ```docker compose up``` then got some data in the homeycomb home page 
    
    <img width="1440" alt="Screen Shot 2023-03-08 at 7 27 40 PM" src="https://user-images.githubusercontent.com/63635704/223786278-bc186e76-6813-4f5b-b317-0e6a827cde51.png">
    
    I open port 4567 & 3000 the open both url then refreshed the honeycomb ui to get some data
    
    
    <img width="1426" alt="Screen Shot 2023-03-08 at 7 21 15 PM" src="https://user-images.githubusercontent.com/63635704/223787100-06f6a697-b841-4cfe-9bc3-72841bd0377f.png">
    
    
    Ran new query for concurrency in honeycomb
    
   <img width="1427" alt="Screen Shot 2023-03-08 at 7 34 35 PM" src="https://user-images.githubusercontent.com/63635704/223787870-fdcb73aa-eaa0-4ee2-baca-8429a1888d48.png">




- Run queries to explore traces within Honeycomb.io

- Instrument AWS X-Ray into backend flask application

- Configure and provision X-Ray daemon within docker-compose and send data back to X-Ray API

- Observe X-Ray traces within the AWS Console

- Integrate Rollbar for Error Logging

- Trigger an error an observe an error with Rollbar

- Install WatchTower and write a custom logger to send application log data to - CloudWatch Log group




