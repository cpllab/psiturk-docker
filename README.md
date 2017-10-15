# Version-stable psiTurk Docker images

This repository provides Docker images for psiTurk, via
[DockerHub][adamliter/psiturk].

According to the [documentation][what-is-psiturk], psiTurk is:

> designed to help you run fully-customized and dynamic web-experiments
> on [Amazon Mechanical Turk]. Specifically, it allows you to:
>
> 1. Run a web server for your experiment
> 2. Test your experiment
> 3. Interact with [Amazon Mechanical Turk] to recruit, post [Human
>    Intelligence Task]s, filter, and pay participants ([Amazon
>    Mechanical Turk] workers)
> 4. Manage databases and export data
>
> **psiTurk** also includes a powerful interactive command interface
> that lets you manage most of your [Amazon Mechanical Turk] activity.


## Tags

The images are tagged using psiTurk version numbers, as well as the
tag `latest` for the most recent version of psiTurk. The current tags
are:

- `latest`
- `2.2.3`
- `2.2.2`
- `2.2.1`
- `2.2.0`
- `2.1.7`
- `2.1.6`

## Usage

These Docker images are intended to be the base for your own (very
simple!) Docker images.

To get started, first [install Docker][docker-install].

Next, create a directory for your experiment:

``` sh
mkdir stroop-task
cd stroop-task
```

Then, create a Dockerfile (replace `latest` with whatever version of
psiTurk that you want to use; see the available [tags above][tags]):

``` sh
echo "FROM adamliter/psiturk:latest" > Dockerfile
```

You have two options about how to set up your Docker image. You can
*not* include the experiment files in the image and instead keep the
experiment files on the host machine, mounting the directory containing
the experiment files to the Docker container.

Alternatively, you can include the experiment files in the Docker image
itself.

With the first option, you will be able to edit the files on your local
host machine, and these changes will be reflected in the experiment that
is being served by the Docker container without having to rebuild the
image. I'd suggest this option for developing and tweaking an
expeirment.

With the second option, the files are copied into the Docker image, and
so the Docker image has to be rebuilt each time you make a change to the
experiment files. I'd suggest this option for deployment of the final
version of your experiment and/or distributing it to other researchers.

In either case, let's use the example experiment generated by running
the command `psiturk-setup-example`. So, run the command:

``` sh
psiturk-setup-example
```

This will create a directory called `psiturk-example` with the example
experiment files. Unless you already have a `~/.psiturkconfig` file, it
most likely also created this file, too.

The Docker image expects the `.psiturkconfig` file to be inside the
same directory as the experiment files, so run the following command:

``` sh
mv ~/.psiturkconfig ./psiturk-example/
```

### Mounting a volume

Now, to set up your Dockerfile for the first option, run the following
command:

``` sh
echo 'VOLUME ["/psiturk"]' >> Dockerfile
```

Then, build your Docker image:

``` sh
docker build -t <YOUR_USERNAME>/stroop-task .
```

And, finally, run a container from the built image:

``` sh
docker run -it --rm --name stroop-task -p 22362:22362 -v `pwd`/psiturk-example:/psiturk <YOUR_USERNAME>/stroop-task
```

This will pop you into a bash session where you can start the psiTurk
server:

``` sh
psiturk
server on
```

To check it out, navigate to `http://localhost:22362` in your browser.

To detach from the Docker container without killing it, hit
<kbd>CTRL</kbd>+<kbd>p</kbd> then <kbd>CTRL</kbd>+<kbd>q</kbd>.

Now you can edit your experiment files, and these changes should be
reflected in your browser.

### Including experiment files in image

To set up your Dockerfile for the second option, run the following
command:

``` sh
echo "COPY ./psiturk-example /psiturk" >> Dockerfile
```

Then build your Docker image:

``` sh
docker build -t <YOUR_USERNAME>/stroop-task .
```

And, finally, run a container from the built image:

``` sh
docker run -it --rm --name stroop-task -p 22362:22362 <YOUR_USERNAME>/stroop-task
```

This will pop you into a bash session where you can start the psiTurk
server:

``` sh
psiturk
server on
```

To check it out, navigate to `http://localhost:22362` in your browser.

If you want to make any changes, you will have to rebuild the image
because the experiment files are inclued in the image.

## Troubleshooting

If you're having trouble getting things to work, try changing the
default `host` setting in `config.txt` from `localhost` to `0.0.0.0`.
For some reason, the `localhost` option has never worked for me, but
`0.0.0.0` does.


[what-is-psiturk]: http://psiturk.readthedocs.io/en/latest/forward.html#what-is-psiturk
[adamliter/psiturk]: https://hub.docker.com/r/adamliter/psiturk/
[docker-install]: https://docs.docker.com/engine/installation/
[tags]: #tags


<!-- Local Variables: -->
<!-- mode: gfm -->
<!-- coding: utf-8 -->
<!-- fill-column: 72 -->
<!-- End: -->
