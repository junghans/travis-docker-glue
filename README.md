[![Build Status](https://travis-ci.org/junghans/travis-docker-glue.svg?branch=master)](https://travis-ci.org/junghans/travis-docker-glue)
[![codecov.io](https://codecov.io/github/junghans/travis-docker-glue/coverage.svg?branch=master)](https://codecov.io/github/junghans/travis-docker-glue?branch=master)

# Summary

This repository contains the glue code to use docker to run CI through
Travis.

Reason to use Docker in Travis:
- Testing other Linux Distributions
- Dependencies, which cannot be not packaged and/or need a long time to build
- Need to deploy your build on docker hub

# Supported Features

- [Travis' ccache](https://docs.travis-ci.com/user/caching/#ccache-cache)
- [codecov.io](https://codecov.io/gh/junghans/travis-docker-glue)
- [docker deploy](https://hub.docker.com/r/junghans/travis-docker-glue/tags)
- [doxygen deploy](http://junghans.github.io/travis-docker-glue)

# Release

This software has been approved for open source release (BSD) as part of FleCSI under **LA-CC-16-022**.
