# continuous-delivery-docs-example

Docs as code example that renders docs written in markdown to
  * Plain PDF (using [sindresorhus/gulp-markdown-pdf](https://github.com/sindresorhus/gulp-markdown-pdf)) - `yarn pdf`,
  * ODT (using [pandoc](http://pandoc.org/) via [cloudogu/pandoc](https://hub.docker.com/r/cloudogu/pandoc) docker image) `yarn odt`,
  * Cloudogu corporate design (using [cloudogu/doc_template](https://hub.docker.com/r/cloudogu/doc_template) that bases on [cloudogu/pandoc](https://hub.docker.com/r/cloudogu/pandoc)) - `yarn cloudogu-pdf`.

Don't forget to call `yarn install` once, before building documents.

When using this with a Cloudogu Ecosystem, you can edit slides conveniently from
[Smeagol](https://github.com/cloudogu/smeagol).

Combined with the Jenkins Job (see [`Jenkinsfile`](Jenkinsfile)), every save in Smeagol triggers the Continuous Delivery 
pipeline, which creates PDF and ODT and appends them to the Jenkins Job.