FROM ruby:3.3.4-slim

FROM ruby:latest

RUN gem install webrick

WORKDIR /app
COPY . .

CMD ["ruby", "-run", "-e", "httpd", ".", "-p", "8081"]

# RUN groupadd -r ruby && useradd -r -g ruby ruby
# WORKDIR /usr/src/app
# RUN chown -R ruby:ruby /usr/src/app