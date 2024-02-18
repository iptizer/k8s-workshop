# k8s-workshop

This is the repository for the k8s-workshop to be first held on the Easterhegg21 in 2024 in Regensburg.

Tutorial can be found here: [https://iptizer.github.io/k8s-workshop/](https://iptizer.github.io/k8s-workshop/)

## Development

Repository setup needs to be done before continuing here.

```sh
# run local dev server
mkdocs serve -a 127.0.0.1:8080
```

## Deploy

GitHub Actions is configured and a push to `main` will redeploy to GitHub pages.

## Repository setup

For developing this documentation the repostiory needs to be set up. See the following steps.

```sh
# pre-commit needs to be installed
pre-commit install
# python3 needs to be installed
python -m pip install -r requirements.txt
```

## Licence

Copyright 2024 Moritz Graf

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
