# gitlab-command

stages:
  - build
  - gitlab-register
build_test_image:
  stage: build
  script:
    - mvn -v
    - mvn clean install -U compile -DskipTests=true
    - docker build -t kaps-backend .    
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" http://127.0.0.1:9002/repository/docker-repo/
    - docker tag kaps-backend 127.0.0.1:9002/repository/docker-repo/kaps-backend 
    - docker push 127.0.0.1:9002/repository/docker-repo/kaps-backend
  only:
    - test
  tags:
    - test-runner
gitlab-register:
  stage: gitlab-register
  script:
    - docker login git.hakase-labs.pw:5050 -u CI_GITLAB_TOKEN -p "$CI_GITLAB_TOKEN" 
    - docker push git.hakase-labs.pw:5050/kaps-backend
  only:
    - test
  tags:
    - test-runner    
