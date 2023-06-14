
```
cd software

# amq-streams-2.4.0-bin.zip

curl -JLOk https://github.com/prometheus/prometheus/releases/download/v2.37.8/prometheus-2.37.8.linux-amd64.tar.gz
curl -JLOk https://dl.grafana.com/oss/release/grafana-10.0.0.linux-amd64.tar.gz

cd ..

buildah build -t zk1 -f Containerfile-zookeeper --build-arg MYID=1
buildah build -t zk2 -f Containerfile-zookeeper --build-arg MYID=2
buildah build -t zk3 -f Containerfile-zookeeper --build-arg MYID=3

podman network create kafka

podman run --name zk1 --rm --network kafka --hostname zk1 --detach --interactive --tty zk1
podman run --name zk2 --rm --network kafka --hostname zk2 --detach --interactive --tty zk2
podman run --name zk3 --rm --network kafka --hostname zk3 --detach --interactive --tty zk3

buildah build -t k1 -f Containerfile-kafka --build-arg ID=1
buildah build -t k2 -f Containerfile-kafka --build-arg ID=2
buildah build -t k3 -f Containerfile-kafka --build-arg ID=3

podman run --name k1 --rm --network kafka --hostname k1 --detach --interactive --tty k1
podman run --name k2 --rm --network kafka --hostname k2 --detach --interactive --tty k2
podman run --name k3 --rm --network kafka --hostname k3 --detach --interactive --tty k3

buildah build -t prom -f Containerfile-prometheus

podman run --name prom --rm --network kafka --hostname prom --detach --interactive --tty prom

buildah build -t graf -f Containerfile-grafana

podman run --name graf --rm --network kafka --hostname graf --detach --interactive --tty --publish 3000:3000 graf
```
