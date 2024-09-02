FROM ruby:3.3.4-slim

# Install necessary build tools and libraries
RUN apt-get update && apt-get install -y \
    build-essential \
    libmariadb-dev-compat \
    libmariadb-dev \
    libssl-dev \
    libreadline-dev \
    zlib1g-dev \
    libxml2-dev \
    libxslt1-dev \
    default-mysql-client \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY backend/Gemfile backend/Gemfile.lock ./
RUN gem install bundler && bundle install
COPY backend ./
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]