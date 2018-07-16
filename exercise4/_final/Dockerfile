FROM ruby:2.5.1

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

RUN mkdir /app
WORKDIR /app

COPY ./blog/Gemfile /app/Gemfile
COPY ./blog/Gemfile.lock /app/Gemfile.lock
RUN bundle install

COPY ./blog /app

CMD ["bundle", "exec", "rails", "s", "-p", "3000", "-b", "'0.0.0.0'"]