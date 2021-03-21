# SnmpCollector Documentation

This repository contains both the content and the static-site generator code for the
snmpcollector documentation site.

## Contributing Changes

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for general instructions for new contributors.

The main documentation contents of this website are located in the [`content/docs`](content/docs) directory.

As a guideline, please keep the documentation generally applicable and avoid use-case-specific changes.

## Prerequisites

This documentation is powered using the hugo framework and uses Doks theme that uses npm to install dependencies and run commands. Installing npm is pretty simple. Download and install Node.js (it includes npm) for your platform.


## Building

To generate the static site, run:

1. Clone the repo

```bash
git clone https://github.com/toni-moreno/snmpcollector.org.git && cd snmpcollector.org
```

2. Install dependencies

```bash
npm install
```

3. Start development server

```bash
npm run start
```


## Development Server

To run a local server that displays the generated site, run:

```bash
npm run start
```

You should now be able to view the generated site at
[http://localhost:3000/](http://localhost:3000).

## Automatic Deployment

This site is automatically deployed using [Github Actions](https://docs.github.com/es/actions).

TODO

## Documentation

- [Github Actions](https://docs.github.com/es/actions)
- [Hugo](https://gohugo.io/documentation/)
- [Doks](https://getdoks.org/)

## License
This software is released under the MIT License, see LICENSE.
