# nettai-ai

The trained network that plays the practice dummy's **Fights** in [nettai](https://github.com/nettaisen/nettai).
nettai downloads it from this repository's site, so a better network reaches players without a new release of the app.

## What is here

The networks aren't in git. Each one is a **release** of this repository, tagged `<line>-r<revision>` (`0.11-r1`),
holding `nettai-ai.safetensors`: the network for a nettai **line**, its major and minor version such as `0.11`
(safetensors: the tensors, and what nettai reads to play them, such as the observation spec they were trained on).

When a release is published, `.github/workflows/pages.yml` rebuilds the site, https://nettaisen.github.io/nettai-ai/:

- `index.json`: each line's newest network, by line: its SHA-256, so nettai can tell whether the copy it keeps is
  current without downloading the network, and its full address.
- `weights/<sha256>.safetensors`: those networks, named by their SHA-256.

A nettai of version `0.11.x` plays the `0.11` line's newest network. A new minor version of nettai may change what
the network sees, so it waits for a network trained for it, published under its own line.

## How nettai uses it

In a build with its `ai` feature, the first time Fights is picked in a run nettai reads the site's `index.json`
(whose address nettai's catalog, https://nettai.n1gp.net/catalog.json, gives),
downloads its line's network when the copy it keeps isn't that one (checked by SHA-256), and keeps it in its data
folder (`ai/<line>/`). With no connection it plays the copy kept. Weights it can't play (trained on another
observation spec) are refused.

To try other weights without publishing them, run nettai with `NETTAI_AI_WEIGHTS=<file.safetensors>`.

## Publishing

From nettai's `training/` folder, with the GitHub CLI signed in:

```
uv run python -m nettai_ai.publish runs/NAME/checkpoints/ckpt_NNNNNNN.pt [--line 0.11] [--release]
```

It exports the checkpoint and runs nettai's tests that need trained weights on the file. With `--release` it
publishes the file as this repository's next release of the line, which rebuilds the site. See nettai's
`docs/design/training-ai.md` §10.5.
