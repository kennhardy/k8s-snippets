FROM ruby:2.6

WORKDIR /usr/src/app
COPY Gemfile ./

RUN gem install bundler:2.3.17
RUN bundle install
COPY . .
CMD ["bundle", "exec", "thin", "-R", "config.ru", "start"]

EXPOSE 3000
ENV RACK_ENV prod
ENV ONETIMESECRET_SSL=false \
    ONETIMESECRET_HOST=localhost:3000 \
    ONETIMESECRET_SECRET=CHANGEME \
    ONETIMESECRET_REDIS_URL= \
    ONETIMESECRET_COLONEL=
