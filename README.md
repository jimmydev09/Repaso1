//traefik.yml
api:
  insecure: true
  dashboard: true
entryPoints:
  http:
    address: ":80"
  traefik:
    address: ":8888"
providers:
  file:
    filename: app-static.yml
    watch: true


//app-static.yml
http:
  routers:
    router-app-authors:
      entryPoints:
        - http
      rule: PathPrefix(`/app-authors`)
      service: service-app-authors
      middlewares:
        - strip-prefix-authors

    router-app-books:
      entryPoints:
        - http
      rule: PathPrefix(`/app-books`)
      service: service-app-books
      middlewares:
        - strip-prefix-books

  services:
    service-app-authors:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:8080"
          - url: "http://127.0.0.1:8081"

    service-app-books:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1:9090"
          - url: "http://127.0.0.1:9091"
          - url: "http://127.0.0.1:9092"
  middlewares:
    strip-prefix-authors:
      stripPrefix:
        prefixes:
          - "/app-authors"

    strip-prefix-books:
      stripPrefix:
        prefixes:
          - "/app-books"





/////
java -Dquarkus.http.port=8080 -jar app-authors/build/quarkus-app/quarkus-run.jar

java -Dquarkus.http.port=9090 -jar app-books/build/quarkus-app/quarkus-run.jar
