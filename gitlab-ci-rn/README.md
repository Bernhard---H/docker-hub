## Example usage:

``` yml
# .gitlab-ci.yml

stages:
  - build

build:
  stage: build
  image: bernhard/gitlab-ci-react-native-android

  cache:
    key: ${CI_PROJECT_ID}
    paths:
      - android/.gradle/
      - node_modules
  before_script:
    - git submodule sync --recursive
    - git submodule update --init --recursive
    - yarn
    # make sure you added an environment variable with your base64 encoded keystore
    - echo $ANDROID_KEYSTORE > keystore.base64
    # you may need to change the name of the key store file:
    - base64 --decode keystore.base64 > ./android/app/my-release-key.keystore
    - mkdir myapp
  script:
    - cd android
    - ./gradlew assembleRelease
    - cp app/build/outputs/apk/app-release.apk ../myapp/myapp-release.apk
  artifacts:
    paths:
      - myapp/
    expire_in: 1 weeks
    name: myapp

```


