FROM laristra/flecsi-buildenv:fedora

ARG COVERAGE
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

RUN rm -rf /home/flecsi/.ccache
COPY flecsi /home/flecsi/flecsi
COPY ccache/ /home/flecsi/.ccache
USER root
RUN chown -R flecsi:flecsi /home/flecsi/flecsi /home/flecsi/.ccache
USER flecsi

WORKDIR /home/flecsi/flecsi
RUN mkdir build

WORKDIR build
RUN ccache -z
RUN cmake ${COVERAGE:+-DENABLE_COVERAGE_BUILD=ON} ..
RUN make VERBOSE=1 -j2
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
USER root
RUN make install
USER flecsi
WORKDIR /home/flecsi
