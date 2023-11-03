Retirement simulation dashboard
===============================

This is a retirement savings simulation dashboard, built with [Quarto](https://quarto.org/) and [Shiny](https://shiny.posit.co/py/) for Python. Quarto provides the document structure and layout, and Shiny provides the interactivity.

[![Screenshot of retirement simulation dashboard](./retirement-gallery.png)](https://wch.github.io/retirement-simulation-dashboard/)

[Live dashboard](https://wch.github.io/retirement-simulation-dashboard/)

[Learn more](https://quarto.org/docs/dashboards/interactivity/shiny-python/index.html) about using Quarto and Shiny to create interactive dashboards


## Deployment to a static web host

This Quarto dashboard uses Shiny for interactivity and is deployed as a static web page to GitHub Pages.

A _static_ Quarto dashboard uses Python or R to do computation at _render time_. The result is a static web page which can be deployed to a static web host, such as GitHub Pages.

An _interactive_ Quarto dashboard with Shiny for Python requires a server running Python, because it also does computation at _run time_: when a user interacts with the dashboard, it runs Python code in response to the actions. Normally these interactive dashboards would be deployed on a platform with servers running Python, such as [shinyapps.io](https://www.shinyapps.io/) (Posit's hosted platform) or [Posit Connect](https://posit.co/products/enterprise/connect/) (Posit's on-premises server platform).

However, in many cases a Quarto dashboard with Shiny can also be deployed to a static web host (with some limitations discussed below). This is done by using [Shinylive](https://shiny.posit.co/py/docs/shinylive.html), which is a special deployment mode of Shiny where, instead of running Python on a remote server, _Python itself runs in the web browser_. This means that the dashboard can be deployed as a web page to any static web host, such as GitHub Pages. The static web host does not need to run Python -- it simply serves up files and the user's web browser runs the Python code.

The dashboard in this repository is deployed as a Shinylive app to GitHub Pages.

There are some limitations of Shinylive:

- Not all Python packages can run in the web browser.
- No secrets: all data and code is sent to the web browser, so it is not appropriate if your code or data contains any information that you want to keep private.

See the [Shinylive documentation](https://shiny.posit.co/py/docs/shinylive.html) for more details.


### Deployment instructions

To deploy this dashboard to a static web host, first make sure [Quarto](https://quarto.org/docs/download/) (version 1.4.479 or higher) is installed. Then install the shinylive Python package. This package provides a command line tool named `shinylive`, which is used to convert a normal Shiny app to a Shinylive web page.

```bash
pip install shinylive --upgrade
```

Next, create your Quarto dashboard. When you have finished working on your Quarto dashboard, run the following:

```bash
quarto render --output-dir _build
```

This will generate the following files in `_build/`:

- `retirement.html` - the static HTML file for the dashboard
- `retirement_files/` - a directory containing JS and CSS files for the dashboard
- `app.py` - a Python file with the Shiny code

These files collectively constitute a Shiny application. You can run this as a normal Shiny application with `shiny run` to make sure it works as expected.

Now we'll convert it to a static web page using the `shinylive` command line tool:

```bash
shinylive export _build _site
```

This will generate a Shinylive bundle in `_site/`. This directory contains the following files:

- `index.html` - the main HTML file for the dashboard
- `shinylive/` - a directory containing Shinylive web assets
- `app.json` - a JSON file that contains all the code and data for the dashboard app, bundled into a single file

You can view the app locally by running:

```bash
python3 -m http.server --directory _site --bind localhost 8008
```

Then visit http://localhost:8008/ in a web browser. If that looks good, you can deploy the `_site/` directory to your static web host. That directory contains everything needed to run the dashboard in a web browser.


### Automatically deploying to GitHub Pages with GitHub Actions

You can deploy your dashboard every time you push to a repository, with GitHub Actions. This repository has a GitHub Actions workflow that does just that. If you would like to use it:

- Ensure that you have a `requirements.txt` that is up to date with your Python dependencies. You can generate this file by running:
    ```bash
    pip freeze > requirements.txt
    ```
- In the **Settings** tab of your repository, select **Pages** in the sidebar, and then under **Build and deployment** set the **Source** to **GitHub Actions**.
- Copy the file [`.github/workflows/build-deploy.yml`](.github/workflows/build-deploy.yml) to the same location in your repository, then commit it and push.

It will then start building and deploying your dashboard. You can view the progress in the **Actions** tab of your repository.

If the page successfully deploys, you can view it at `https://<username>.github.io/<repo-name>/`. For example, the dashboard in this repository is deployed at https://wch.github.io/retirement-simulation-dashboard/.

