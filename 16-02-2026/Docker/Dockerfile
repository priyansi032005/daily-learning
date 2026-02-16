Docker file ---> Docker image (Blue print)---> Docker Container (isolated env)

PYTHONUNBUFFERED 1\
PYTHONDONTWRITEBYTECODE=1 \.  ---> prevent creating .pyc files
PIP_NO_CACHE_DIR=1 \.  ---> Prevents pip from storing downloaded package files in cache
PIP_DISABLE_PIP_VERSION_CHECK=1.  --- Disables pip’s automatic version check

commands:
    ENV
    WORKDIR /Backend
    RUN
    COPY
    CMD
    EXPOSE make available port for use
    RUN mkdir static logs (that will make 2 folders in work directory)

hoe to use images from docker hub 

then copy file install all the dependency from requirements.txt or somewhere else
then RUN: run the dependency

we can use entrypoint.sh : in which we can add commands of migrations and runserver

