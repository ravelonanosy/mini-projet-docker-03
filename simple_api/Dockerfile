FROM python:3.8-bullseye
MAINTAINER nanja (romario117910@gmail.com)

RUN apt-get update
#RUN apt-get -y install gcc
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install build-essential python3-dev libldap2-dev libsasl2-dev slapd ldap-utils tox lcov valgrind

WORKDIR /

COPY requirements.txt /

RUN pip3 install -r requirements.txt

COPY . /

EXPOSE 5000

CMD ["python", "./student_age.py"]
