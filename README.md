# Datascience Docker Image

Dockerfile for `jupyter/scipi-notebook` modified by:
[Pranav Kumar Mishra](https://drpranavmishra.com) to add the following features:

- Python version 3.9 (not 3.10 which is the current version on `jupyter/scipi-notebook` and had breaking changes)
- [Datalad](https://github.com/datalad/datalad) - version controlling large datasets
- [Git-Annex](https://git-annex.branchable.com/) - included with Datalad
- [Nbdime](https://github.com/jupyter/nbdime) - easy to visualized diffs for jupyter notebooks
- [Pre-Commit v2.20.0](https://github.com/pre-commit/pre-commit) - checks on code before commiting changes
- [Zsh](https://www.zsh.org/) - A newer shell
- [yadm](https://github.com/TheLocehiliosan/yadm) - yet another dotfile manager
- [GnuPG](https://gnupg.org/) - PGP encryption
- [Rclone](https://github.com/rclone/rclone)
- [git-annex-remote-rclone](https://github.com/DanielDent/git-annex-remote-rclone) - Use rclone as a git-annex remote
- [Exa](https://the.exa.website/) - a newer form of `ls`
- [Unzip](https://pkgs.org/download/unzip) - unzip compressed `.zip` files
- [jupyterthemes](https://github.com/dunovank/jupyter-themes) - theme for jupyter notebooks
- [Primer3](https://github.com/primer3-org) - create RT-qPCR primers

## Development

First, run the following commands

```
pre-commit install
```
## Building the image

Build the first image. The `CACHEBUST` is looking for the line `ARG CACHEBUST=1`, which breaks the stored image layers. This has been inserted prior to the major modifications I have made, deviating from jupyter's image.

```sh
# cd docker
docker build -t datascience:nightly -t gitea.mishracloud.com/rush/datascience:nightly -t pranavmishra90/datascience:nightly --build-arg CACHEBUST=$(date +%s) .
```

Push to Gitea
```
docker push gitea.mishracloud.com/rush/datascience:nightly
```
After testing, create the additional tags


```sh
docker tag datascience:nightly pranavmishra90/datascience:v0.0.<increment number here>
```

Push to DockerHub
```
docker push --all-tags datascience
```

## Usage

Testing the container: mounting in current directory
```
datalad-here='docker run --name datalad -p 8888:8888 --user root -e GRANT_SUDO=yes \\\n\t-v "${PWD}":/home/coder/lab -v ~/bfi-datalad/git-annex:/home/coder/git-annex datascience:nightly'
```

### Git-Annex

#### Rclone remotes
```
cat /home/coder/.config/rclone/rclone.conf

[git-annex]
type = alias
remote = /home/coder/git-annex
```

```
git annex initremote rushdrive-annex type=external externaltype=rclone target=rushdrive-annex prefix=bone-fat-insulin-project chunk=50MiB rclone_layout=lower encryption=none

initremote rushdrive-annex ok
(recording state in git...)
```

```
datalad siblings configure -s gitea --publish-depends rushdrive-annex
```
