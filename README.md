# Example of workflow using snyk fix and create-pull-request

This workflow run snyk fix and open a PR with snyk fix changes.
To avoid incrementing the versions from more than one this workflow starts by checking the last commit message to avoid re-running snyk fix if the last commit was already an automated fix.

Here are the steps to require to do this (full example under .github/workflows/main.yml) :
Please make sure to add the SNYK_TOKEN to your repository and change the 'base' branches in each steps to match your configuration.

     jobs:
        init:
          runs-on: ubuntu-latest
          outputs:
            skip: ${{ steps.ci-skip-step.outputs.ci-skip }}
            skip-not: ${{ steps.ci-skip-step.outputs.ci-skip-not }}
          steps:
            - uses: actions/checkout@v2
              with:
                fetch-depth: 0
            - id: ci-skip-step
              uses: mstachniuk/ci-skip@master
              with:
                commit-filter: 'Merge pull request'
        security:
          runs-on: ubuntu-latest
          needs: init
          if: ${{ needs.init.outputs.skip == 'false' }}
          steps:
            - uses: actions/checkout@master
            - name: Run Snyk to check for vulnerabilities
              uses: snyk/actions/python-3.8@master
              env:
                SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
              with: 
                command: fix # adding this option to run snyk fix
                base: master    
            - name: create a PR
              uses: peter-evans/create-pull-request@v3
              with:
                base: test # pull request base branch
            - name: get PR info
              run: |
                echo "Pull Request Number - ${{ steps.cpr.outputs.pull-request-number }}"
                echo "Pull Request URL - ${{ steps.cpr.outputs.pull-request-url }}"      
            - name: Run a multi-line script
              run: |
                echo Add other actions to build,
                echo test, and deploy your project.
