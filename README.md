# maven-in-docker
docker in docker include maven

docker run -d --name gitlab-runner-dockersock \
 --restart always \
 -v /var/run/docker.sock:/var/run/docker.sock \
 -v /etc/gitlab-runner:/etc/gitlab-runner \
 gitlab/gitlab-runner:alpine

docker exec -it gitlab-runner-dockersock gitlab-runner register -n  --url http://<GITLAB_EXTERNAL_URL>/  \
 --registration-token <TOKEN>   \
 --description "dockersock"  --docker-privileged=true  --docker-pull-policy="if-not-present" \
 --docker-image "docker:latest" \
 --docker-volumes /var/run/docker.sock:/var/run/docker.sock \
 --docker-volumes /root/.m2:/root/.m2 \
 --executor docker 


#.gitlab-ci.yml
image: longkeyy/docker-maven:latest

stages:
  - test
  - build

test app:
  stage: test
  script:
    - mvn test

build app:
  stage: build
  script:
    - mvn  -Dmaven.test.skip=true clean package docker:build

