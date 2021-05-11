TAG="cni_bert:latest" 

docker build -t $TAG . 

docker run --rm -it -v $(pwd):/app -p 8888:8888 $TAG

[comment]: <> (docker run --rm -it $TAG bash)

[comment]: <> (docker run --rm -idt $TAG bash)

