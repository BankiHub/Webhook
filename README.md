# Webhook


To trigger GitHub workflows in another repository using a webhook, you can use the repository_dispatch event. This event allows you to trigger a workflow in a different repository by sending a custom event payload. Here's a step-by-step guide:

1. Create a Workflow in the Target Repository:
In the repository where you want to trigger the workflow, create a workflow file (e.g., .github/workflows/my_workflow.yml).

yaml
Copy code
name: My Workflow

on:
  repository_dispatch:
    types:
      - trigger-workflow-event
This workflow listens for the repository_dispatch event with the custom type trigger-workflow-event. You can customize the types field to match the event type you want to use.

2. Configure a Personal Access Token (PAT):
To trigger workflows in another repository, you'll need a Personal Access Token (PAT) with the necessary permissions. Create a PAT with at least the repo scope.

3. Add PAT as a Secret:
In the repository where the webhook will be triggered, go to Settings > Secrets and add a new secret with the PAT you generated. Let's assume you name it DOWNSTREAM_REPO_TOKEN.

4. Create a Webhook in the Source Repository:
In the repository that will trigger the event (source repository):

Go to Settings > Webhooks > Add webhook.
Set the Payload URL to the repository_dispatch endpoint for the target repository: https://api.github.com/repos/:owner/:repo/dispatches.
Set the Content type to application/json.
In the Secret field, you can leave it empty or use a secret (optional).
Set up other configurations according to your needs.
In the "Which events would you like to trigger this webhook?" section, choose "Let me select individual events" and select only the Repository dispatch event.
Save the webhook.
5. Trigger the Webhook:
To trigger the workflow in the target repository, you need to send a repository_dispatch event using the GitHub API. This can be done using curl or other HTTP clients.

Here's an example using curl:

bash
Copy code
REPO_OWNER=your-username
REPO_NAME=your-repository
EVENT_TYPE=trigger-workflow-event

curl -X POST \
  -H "Authorization: token ${{ secrets.DOWNSTREAM_REPO_TOKEN }}" \
  -H "Accept: application/vnd.github.everest-preview+json" \
  -H "Content-Type: application/json" \
  -d '{"event_type": "'$EVENT_TYPE'"}' \
  "https://api.github.com/repos/$REPO_OWNER/$REPO_NAME/dispatches"
Replace your-username and your-repository with the owner and name of the target repository, and adjust the EVENT_TYPE as needed.

6. Handle the Triggered Workflow:
Now, when the webhook is triggered, the workflow in the target repository (my_workflow.yml) will run in response to the repository_dispatch event.

Remember to handle secrets, authentication, and permissions carefully to ensure the security of your workflows and repositories.
