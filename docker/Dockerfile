FROM junghans/travis-docker-glue:fedora
#see the Dockerfiles branch for source of these

ARG COVERAGE
ARG SONARQUBE
ARG SONARQUBE_TOKEN
ARG CC
ARG CXX

#for coverage
ENV CI true
ENV TRAVIS true
ARG TRAVIS_BRANCH
ARG TRAVIS_JOB_NUMBER
ARG TRAVIS_PULL_REQUEST 
ARG TRAVIS_JOB_ID
ARG TRAVIS_TAG
ARG TRAVIS_REPO_SLUG
ARG TRAVIS_COMMIT

RUN rm -rf /home/user/.ccache
COPY git-src /home/user/git-src
COPY ccache/ /home/user/.ccache
COPY sonar/ /home/user/.sonar
USER root
RUN chown -R user:user /home/user/git-src /home/user/.ccache /home/user/.sonar
USER user

WORKDIR /home/user/git-src
RUN mkdir build

WORKDIR build
RUN ccache -z
RUN cmake ${COVERAGE:+-DENABLE_COVERAGE_BUILD=ON} -DENABLE_TESTING=ON ..
RUN ${SONARQUBE:+build-wrapper-linux-x86-64 --out-dir bw-output} make VERBOSE=1 -j2
RUN ccache -s
RUN make test
RUN make install DESTDIR=${PWD}
RUN cd .. && if [ ${COVERAGE} ]; then \
  if [ ${CC} = clang ]; then \
    $HOME/.local/bin/codecov --gcov-exec "llvm-cov gcov"; \
  else \
    $HOME/.local/bin/codecov; \
  fi; \
fi && cd -
RUN cd .. && if [ ${SONARQUBE} ]; then \
  sonar-scanner \
    -Dsonar.projectKey=${TRAVIS_REPO_SLUG##*/} \
    -Dsonar.sources=. \
    -Dsonar.exclusions=build/CMakeFiles/** \
    -Dsonar.cfamily.build-wrapper-output=build/bw-output \
    -Dsonar.host.url=https://sonarqube.com \
    -Dsonar.organization=${TRAVIS_REPO_SLUG%%/*}-github \
    -Dsonar.login=${SONARQUBE_TOKEN}; \ 
fi && cd -
ENV SONARQUBE_TOKEN ""
USER root
RUN make install
USER user
WORKDIR /home/user
