FROM ruby:3.3.4-slim
WORKDIR /app
COPY backend/Gemfile backend/Gemfile.lock ./
RUN bundle install
COPY backend ./
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]