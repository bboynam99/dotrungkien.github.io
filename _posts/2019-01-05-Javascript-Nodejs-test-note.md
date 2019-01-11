---
layout: post
title: Javascript & Nodejs test note
---

- ![](https://cdn-images-1.medium.com/max/880/1*TRAT1glkPpDFhv-GHCFBNQ.jpeg)

### Test framework/library

- [Mocha](https://mochajs.org/) - the fun, simple, flexible JavaScript test framework.
- [Jest](https://jestjs.io/) - Delightful JavaScript Testing.
- [tape - npm](https://www.npmjs.com/package/tape) - tap-producing test harness for node and browsers.
- [tape-promise](https://www.npmjs.com/package/tape-promise) - Promise and ES2016 (ES7)`async/await`support for- [Tape](https://github.com/substack/tape).
- [supertest: ](https://github.com/visionmedia/supertest) Super-agent driven library for testing node.js HTTP servers using a fluent API.

### Assertion library

- [Chai](https://www.chaijs.com/) - có thể sử dụng tất cả các phong cách test `expect/should/assert`.
- [chai-as-promised - npm](https://www.npmjs.com/package/chai-as-promised/v/5.1.0)
- [expect - npm](https://www.npmjs.com/package/expect) - expect này chính là function `expect` được export từ trong **Jest** của Facebook.
- [should - npm](https://www.npmjs.com/package/should) - ít dùng hơn expect.

### Mock tools

- [node-mocks-http](https://github.com/howardabrams/node-mocks-http)- Mock 'http' objects for testing Express routing functions.
- [Sinon.JS](https://sinonjs.org/) - Standalone test fakes, spies, stubs and mocks for JavaScript. Works with any unit testing framework.
- [nock](https://github.com/nock/nock) : HTTP server mocking and expectations library for Node.js
- [faker.js](https://github.com/marak/Faker.js/) : generate massive amounts of realistic fake data in Node.js and the browser

### ESlint plugin

- [eslint-plugin-chai-expect - npm](https://www.npmjs.com/package/eslint-plugin-chai-expect) - discover test without assertions
- [eslint-plugin-mocha - npm](https://www.npmjs.com/package/eslint-plugin-mocha) - eslint rules for mocha
- [eslint-plugin-jest - npm](https://www.npmjs.com/package/eslint-plugin-jest) - eslint rules for jest
- [eslint-plugin-promise - npm](https://www.npmjs.com/package/eslint-plugin-promise) - discover promises with no resolve
- [eslint-plugin-security - npm](https://www.npmjs.com/package/eslint-plugin-security) - discover eager regex expression that might get used for DOS attack

### property-based testing

- [JSVerify](http://jsverify.github.io/) - property based testing for JavaScript. Like QuickCheck.
- [TestCheck.js](http://leebyron.com/testcheck-js/) - Generative property testing for JavaScript.
- [fast-check](https://github.com/dubzzz/fast-check) - Property based testing framework for JavaScript (like QuickCheck) written in TypeScript.

### analysis code

- [SonarQube](https://www.sonarqube.org/) - Continuous Inspection, Continuous Code Quality.
- [Code Climate](https://codeclimate.com/) - process insights and automated code review.

### Node-related chaos

- [chaosmonkey](https://github.com/Netflix/chaosmonkey) - Chaos Monkey is a resiliency tool that helps applications tolerate random instance failures.
- [kube-monkey](https://github.com/asobti/kube-monkey) - An implementation of Netflix's Chaos Monkey for Kubernetes clusters.

### mutation testing

- [Stryker Mutator](https://stryker-mutator.io/) - Test your tests with mutation testing.

### License check

- [license-checker - npm](https://www.npmjs.com/package/license-checker)

### audit dependencies

- [Auditing package dependencies for security vulnerabilities](https://docs.npmjs.com/auditing-package-dependencies-for-security-vulnerabilities)
- [Snyk](https://snyk.io/) - automates finding & fixing vulnerabilities in your dependencies.
