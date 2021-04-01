# Example usage

```yaml
---
name: Update YARN

on:
  pull_request:

env:
  DOCKER_BUILDKIT: 1
  YARN_CACHE_DIR: /tmp/yarn-cache

jobs:
  update:
    timeout-minutes: 15
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2.3.4

    - name: Setup cache
      uses: actions/cache@v2.1.4
      with:
        path: ${{ YARN_CACHE_DIR }}
        key: ${{ runner.os }}-${{ hashFiles('**/yarn.lock') }}

    - uses: Pararius/action-yarn-upgrade@v0.0.1
      id: yarn-upgrade
      with:
        working-dir: ./
        yarn-cache: ./

    - name: Current date
      run: echo "DATE=$(date +%Y%m%d)" >> $GITHUB_ENV

    - name: Create pull request
      uses: peter-evans/create-pull-request@v3.8.2
      with:
        token: ${{ secrets.GITHUB_TOKEN }}
        branch: php-update-${{ env.DATE }}
        branch-suffix: random
        commit-message: '[ci] updated YARN dependencies'
        title: Updated YARN dependencies
        body: Updated YARN dependencies
        labels: dependencies
        base: master
```
