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

- [One-way git-repo-only sync "self-hosted" => "GitHub"](https://gitlab.lunatic.cat/help/user/project/repository/repository_mirroring.md#setting-up-a-push-mirror-from-gitlab-to-github)
- [One-way git-repo-only sync "github" => "self-hosted"](https://jsramblings.com/automatically-sync-gitlab-mirrored-repository-on-every-push-to-github/)

Pro: easy, sometimes out of the box, also possible with 

Contra: stars/watchers/issues/pull-requests are stored out-of-the-repo, need _personal_ token

- Same `origins` and `.git/hooks` on every pushing host

Pro: no other stuff required

Contra: git-code-only, extra coordination needed

- [Two-way GitHub<=>GitLab sync](https://github.com/IgnoredAmbience/github-gitlab-sync)

Pro: two way

Contra: git-code-only, complicated setup, very opinionated/coupled

## Suggestion overview

- Setup a self-hosted code-management system (GitLab/Gitea/SourceHut/Fossil)
- Create a mirror GitHub repo
- Setup a proxy server listning to Github webhooks, setup hooks
- Proxy server calls Github api as many times as it's required in order to compose a "VCS Sync Message"
- Using a transport layer it forwards it to a sink-proxy
- Sink-proxy is a sidecar container running aside of code-management system, able to access its repos and storage
- Sink-proxy accepts a "VCS Sync Message" and does according storage changes
  basically sink-proxy just allows to create Merge Requests without being registered explicitly
  
![Vision UML](https://raw.githubusercontent.com/0x0-cx/vcs-sync-protocol/master/assets/vision-uml.png)

## VCS Sync Message

- url: domain.com/project/parent-sha
- message: text
- patch

## VCS Sync Protocol (Actions)

- merge