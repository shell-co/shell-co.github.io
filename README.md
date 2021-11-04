# Shell Company CTF Wiki
Contains writeups on ctf problems and the code used to solved them. The search feature makes it easy to go locate similar challenges in the future.

## Run
```
docker run  -v $PWD:/app --workdir /app -p 8000:8000 balthek/zola:0.14.0 serve --interface 0.0.0.0 --port 8000 --base-url localhost
```

## Build
```
docker run  -v $PWD:/app --workdir /app balthek/zola:0.14.0 build
```
