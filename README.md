# A Community Library of Baseline Models

The Community Library publishes tutorials that solve the same economic models
in different toolkits. Contributors write and maintain their own
tutorials, using a shared model statement and calibration so readers can
compare the results.

QuantEcon hosts the [library website](https://quantecon.github.io/community-library/).
The library began as Project B of [SCE Working Group 1 on Language and Formal
Semantics](https://github.com/econ-ark/sce-wg-1) and now runs independently of
the group, which continues to publish the baseline model descriptions.

## Start with a model

Read the [buffer stock model description](models/buffer-stock/template.md)
or its [PDF](models/buffer-stock/template.pdf), which covers model theory,
calibration and numerical concerns. The
[HARK notebook](projects/HARK/BST/tutorial.ipynb) provides a runnable example
with short explanations; its [instructions](projects/HARK/BST/README.md)
give the installation and run commands.

A buffer stock submission contains a runnable notebook, a short record of
the authors and numerical settings, and the result files specified in the
model's [submission list](models/buffer-stock/README.md#what-to-submit).
Include any additional code or environment files needed to run the notebook.

The working group has proposed five baseline models. Projects choose one or
more that their toolkit can solve and also contribute a tutorial on a model
of their own choosing.

| Baseline model | Model class | Description |
| --- | --- | --- |
| [Buffer stock saving](models/buffer-stock/README.md) | Partial-equilibrium consumption–saving under income risk | Draft available |
| Aiyagari · Krusell–Smith | Incomplete-markets heterogeneous agents | In preparation |
| A two-asset HANK model | Heterogeneous-agent New Keynesian | In preparation |
| A small New Keynesian DSGE model | Representative-agent DSGE | In preparation |
| A baseline agent-based macro model | Macro from interacting heterogeneous agents | In preparation |

## Where files belong

| Directory | Purpose |
| --- | --- |
| `models/<model>/` | The shared model description, its PDF and the submission guide. |
| `projects/<toolkit>/<model>/` | A runnable implementation notebook, supporting code and results. |
| `docs/` | The library website. |
| `templates/` | The page layout used to produce model PDFs. |

For example, `projects/HARK/BST/` contains the HARK buffer stock implementation.
Questions about a shared model description belong in
the [working group issue tracker](https://github.com/econ-ark/sce-wg-1/issues).

## Website maintenance

The website consists of static HTML, CSS and JavaScript in `docs/`. Its
appearance follows the [QuantEcon book theme](https://github.com/QuantEcon/quantecon-book-theme).
Edit `docs/index.html` for text, `docs/site.css` for appearance and
`docs/site.js` for the contents navigation.

The [publishing workflow](.github/workflows/publish.yml) checks local file
links, links to sections and the contents navigation on every push and pull
request. It publishes `docs/` to GitHub Pages from `main`; pull requests
provide a downloadable site preview. These checks cover the website;
notebook execution is not yet automated.

Publishing requires **Settings → Pages → Build and deployment → Source:
GitHub Actions**. The [buffer stock guide](models/buffer-stock/README.md)
gives the commands for generating the PDF from the model description.

## Licensing

Text is CC-BY; code is released under an OSI-approved licence. Contributions remain
authored and maintained by their projects. The library publishes the work
with attribution and does not rank the toolkits.
