FROM junghans/travis-docker-glue:fedora
#see the Dockerfiles branch for source of these

ARG COVERAGE
ARG CC
ARG CXX

#for coverage
ARG CI
ARG TRAVIS
ARG TRAVIS_BRANCH
ARG TRAVIS_JOB_NUMBER
ARG TRAVIS_PULL_REQUEST 
ARG TRAVIS_JOB_ID
ARG TRAVIS_TAG
ARG TRAVIS_REPO_SLUG
ARG TRAVIS_COMMIT
ARG TRAVIS_OS_NAME

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
RUN make VERBOSE=1 -j2
RUN ccache -s
RUN make test
RUN make install DESTDIR=${PWD}
RUN make doxygen
RUN cd .. && if [ ${COVERAGE} ]; then \
   python -m coverxygen --xml-dir build/doc/xml/ --src-dir . --output doxygen.coverage.info; \
   wget -O codecov.sh https://codecov.io/bash; \
   bash codecov.sh -X gcov -f doxygen.coverage.info -F documentation; \
   doxy-coverage build/doc/xml/; \
  if [ ${CC} = clang ]; then \
    $HOME/.local/bin/codecov -F ${CC} --gcov-exec "llvm-cov gcov"; \
  else \
    $HOME/.local/bin/codecov -F ${CC}; \
  fi; \
fi && cd -
USER root
RUN make install
USER user
WORKDIR /home/user
