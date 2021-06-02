# Example of workflow using snyk fix and create-pull-request

This workflow run snyk fix and open a PR with snyk fix changes.
We use the skip step to avoid running snyk fix when pushing the automated PR changes as this could result to openning a new PR.

Here are the steps to require to do this (full exampe under .github/workflows/main.yml) :

      steps:
            - uses: actions/checkout@master
            - name: Run Snyk to check for vulnerabilities
              uses: snyk/actions/python-3.8@master
              env:
                SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
              with: 
                command: fix
                base: master
            - name: skip-workflow 
              id: skip-workflow 
              uses: saulmaldonado/skip-workflow@v1.1.0
              with:
                phrase: 'create-pull-request action'
                github-token: ${{ secrets.GITHUB_TOKEN }}
            - name: create a PR
              uses: peter-evans/create-pull-request@v3
              with:
                base: master
            - name: get PR info
              run: |
                echo "Pull Request Number - ${{ steps.cpr.outputs.pull-request-number }}"
                echo "Pull Request URL - ${{ steps.cpr.outputs.pull-request-url }}"      
            - name: Run a multi-line script
              run: |
                echo Add other actions to build,
                echo test, and deploy your project.
