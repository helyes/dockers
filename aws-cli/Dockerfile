FROM alpine:3.9.4

LABEL maintainer="helyesa <helyesa@gmail.com>"

### PYTHON ###
RUN apk --update --no-cache add python3==3.6.8-r2
    
RUN python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    if [ -d /root/.cache ]; then rm -f /root/.cache/* ; fi


### AWS-CLI ###
RUN pip install --upgrade \
    awscli==1.16.160 \
    s3cmd==2.0.2

### UTIL ###
RUN apk add --no-cache \
    nano \
    less \
    sudo

RUN if [ -d /var/cache/apk ]; then rm -f /var/cache/apk/* ; fi 
        
RUN find /usr/lib/python3.6 -depth \( -name '*.pyo' -o -name '*.pyc' -o -name 'test' -o -name 'tests' \) -exec rm -rf '{}' \;

RUN find /usr/lib -type d -name "__pycache__" -prune -exec rm -rf '{}' \;


ENV USER=dockeruser
RUN adduser -D -s /bin/sh "$USER" -G wheel

VOLUME /app
WORKDIR /home/"$USER"

# enable wheel group to sudo
RUN sed -e 's/# %wheel ALL=(ALL) NOPASSWD: ALL/%wheel ALL=(ALL) NOPASSWD: ALL/g' -i /etc/sudoers


USER "$USER"

ARG AWS_ACCESS_KEY_ID_BUILD=dummy_key_id
ENV AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID_BUILD

ARG AWS_SECRET_ACCESS_KEY_BUILD=dummy_secret_key
ENV AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY_BUILD

ARG AWS_DEFAULT_REGION_BUILD=ap-southeast-2
ENV AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION_BUILD

RUN printf "Installed versions\\n"; for app in aws python s3cmd ; do printf "\\t$app: $($app --version)\\n" ; done

ENTRYPOINT ["aws"]
CMD ["help"]