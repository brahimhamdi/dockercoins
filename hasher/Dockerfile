FROM ruby:3.2-alpine
WORKDIR /app
RUN gem install sinatra webrick rackup puma
COPY hasher.rb .
RUN adduser -D hasher && chown -R hasher:hasher /app
USER hasher
EXPOSE 80
CMD ["ruby", "hasher.rb", "-o", "0.0.0.0"]
