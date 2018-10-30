FROM python:3.6-onbuild
ADD . /usr/src/app
WORKDIR /usr/src/app
RUN pip install -r requirements.txt
CMD [ "nameko", "run", "--config", "config.yml", "app" ]
EXPOSE 5000
