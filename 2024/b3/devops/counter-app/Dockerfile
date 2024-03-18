# TODO: The base image must be: python:3.10-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
# copy the requirements.txt into the container with the same name
RUN pip install -r requirements.txt
EXPOSE 5000
# TODO: copy all from the current local folder to the containner 
CMD ["flask", "run"]