# VCS Sync Protocol

## Purpose and motivation

- Email and underlying protocols were and remain to be ok
- GitHub killed [git send-email](https://git-send-email.io/) to exchange patches (and it was ok)
- Pull Requests are awesome, seeing patches in a dedicated webinterface (not email client) is a good thing

But

- You cannot transfer a Pull Request from GitHub to self-hosted GitLab
- Neither can you trasfer Merge Request back to GitHub, or turn to a SourceHut patch

Besides

- GitHub is now being involved in politics: people/businesses/countries are banned
- After being banned on Gmail (given you're using full clients, not just web, which is _easy_)
  you can switch to self-hosted email server easily: all
- After a ban in GitHub (same for vendored GitLab) you still have your code in git
  but you lose _everything_ else (including community)

## Existing approaches

- One-way git-repo-only sync "self-hosted" => "github"

Pro: easy, sometimes out of the box, possible with `.git/hooks`
Contra: stars/watchers/issues/pull-requests are stored out-of-the-repo

## Suggestion overview

- Setup a self-hosted code-management system (GitLab/Gitea/SourceHut/Fossil)
- Create a mirror GitHub repo
- Setup a proxy server listning to Github webhooks, setup hooks
- Proxy server calls Github api as many times as it's required in order to compose a "VCS Sync Message"
- Using a transport layer it forwards it to a sink-proxy
- Sink-proxy is a sidecar container running aside of code-management system, able to access its repos and storage
- Sink-proxy accepts a "VCS Sync Message" and does according storage changes
  basically sink-proxy just allows to create Merge Requests without being registered explicitly

## VCS Sync Message

- url: domain.com/project/parent-sha
- message: text
- patch

## VCS Sync Protocol (Actions)

- merge