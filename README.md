# Due diligence form 
name: Collect Responses

on:
  issues:
    types: [opened, edited]

jobs:
  collect:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Extract issue data
      id: extract
      run: |
        echo "ISSUE_TITLE=$(echo "${{ github.event.issue.title }}" | sed 's/"/\\"/g')" >> $GITHUB_ENV
        echo "ISSUE_BODY=$(echo "${{ github.event.issue.body }}" | sed 's/"/\\"/g')" >> $GITHUB_ENV

    - name: Append to CSV
      run: |
        echo "\"${{ github.event.issue.number }}\",\"${{ env.ISSUE_TITLE }}\",\"${{ env.ISSUE_BODY }}\"" >> responses.csv

    - name: Commit responses
      run: |
        git config --global user.name 'github-actions[bot]'
        git config --global user.email 'github-actions[bot]@users.noreply.github.com'
        git add responses.csv
        git commit -m "Add response from issue #${{ github.event.issue.number }}"
        git push
