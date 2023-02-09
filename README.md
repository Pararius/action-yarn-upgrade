GitHub action to upgrade all NPM dependencies at once, using Yarn. It also creates a diff output if you want to view the upgrades.

## Versioning

By default, Node ships with Yarn V1. But the current (stable) version is V3. This action supports both, use either the v1.x of v3.x tag for this. 

## Example usage

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
    - uses: actions/checkout@v3

    - name: Setup cache
      uses: actions/cache@v3
      with:
        path: ${{ YARN_CACHE_DIR }}
        key: ${{ runner.os }}-${{ hashFiles('**/yarn.lock') }}

    - uses: Pararius/action-yarn-upgrade@v1
      id: yarn-upgrade
      with:
        working-dir: ./
        yarn-cache: ./

    - name: Current date
      run: echo "DATE=$(date +%Y%m%d)" >> $GITHUB_ENV

    - name: Create pull request
      uses: peter-evans/create-pull-request@v4
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
