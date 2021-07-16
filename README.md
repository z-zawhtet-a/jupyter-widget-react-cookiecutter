# jupyter-widget-react-cookiecutter

A [cookiecutter](https://github.com/cookiecutter/cookiecutter) template for a custom
Jupyter widget project in React.

## What is jupyter-widget-react-cookiecutter?

With **jupyter-widget-react-cookiecutter** you can create a custom Jupyter interactive
widget project with sensible defaults using React. jupyter-widget-react-cookiecutter helps custom widget
authors get started with best practices for the packaging and distribution
of a custom Jupyter interactive widget library. This repository is based on [widget-ts-cookiecutter](https://github.com/jupyter-widgets/widget-ts-cookiecutter.git)
with the addition of React integration out of the box and a few useful state management hooks.

## Usage

Install [cookiecutter](https://github.com/audreyr/cookiecutter):

    pip install cookiecutter

After installing cookiecutter, use jupyter-widget-react-cookiecutter:

    cookiecutter https://github.com/Waidhoferj/jupyter-widget-react-cookiecutter.git

As jupyter-widget-react-cookiecutter runs, you will be asked for basic information about
your custom Jupyter widget project. You will be prompted for the following
information:

- `author_name`: your name or the name of your organization,
- `author_email`: your project's contact email,
- `github_project_name`: name of your custom Jupyter widget's GitHub repository,
- `github_organization_name`: name of your custom Jupyter widget's GitHub user or organization,
- `python_package_name`: name of the Python "back-end" package used in your custom widget.
- `npm_package_name`: name for the npm "front-end" package holding the JavaScript
  implementation used in your custom widget.
- `npm_package_version`: initial version of the npm package.
- `project_short_description` : a short description for your project that will
  be used for both the "back-end" and "front-end" packages.

After this, you will have a directory containing files used for creating a
custom Jupyter widget. To check that eveything is set up as it should be,
you should run the tests:

Create a dev environment:

```bash
conda create -n {{ cookiecutter.python_package_name }}-dev -c conda-forge nodejs yarn python jupyterlab
conda activate {{ cookiecutter.python_package_name }}-dev
```

Install the python. This will also build the TS package.

```bash
# First install the python package. This will also build the JS packages.
pip install -e ".[test, examples]"
```

When developing your extensions, you need to manually enable your extensions with the
notebook / lab frontend. For lab, this is done by the command:

```
jupyter labextension develop --overwrite .
yarn run build
```

For classic notebook, you can run:

```
jupyter nbextension install --sys-prefix --symlink --overwrite --py <your python package name>
jupyter nbextension enable --sys-prefix --py <your python package name>
```

Note that the `--symlink` flag doesn't work on Windows, so you will here have to run
the `install` command every time that you rebuild your extension. For certain installations
you might also need another flag instead of `--sys-prefix`, but we won't cover the meaning
of those flags here.

### How to see your changes

#### Typescript:

If you use JupyterLab to develop then you can watch the source directory and run JupyterLab at the same time in different
terminals to watch for changes in the extension's source and automatically rebuild the widget.

```bash
# Watch the source directory in one terminal, automatically rebuilding when needed
yarn run watch
# Run JupyterLab in another terminal
jupyter lab
```

After a change wait for the build to finish and then refresh your browser and the changes should take effect.

#### Python:

If you make a change to the python code then you will need to restart the notebook kernel to have it take effect.

#### Playground

Open the [`introduction.ipynb`](https://github.com/Waidhoferj/jupyter-widget-react-cookiecutter/blob/master/%7B%7Bcookiecutter.github_project_name%7D%7D/examples/introduction.ipynb) notebook to begin developing your widget in the browser.

## Hooks

A few helpful hooks are provided in [`widget-model.ts`](https://github.com/Waidhoferj/jupyter-widget-react-cookiecutter/blob/master/%7B%7Bcookiecutter.github_project_name%7D%7D/src/hooks/widget-model.ts) to sync the state of your React app with the Jupyter Widget model:

- `useModelState` works like the `useState` hook but accepts the name of a Jupyter Widget model property as a string. Setting the state updates the Jupyter Widget model alongside the React state. Most likely this will be the only hook you will ever need from this module.
- `useModelEvent` works like the `useEffect` hook but accepts the name of a Backbone event as its first argument. If you want watch for a Jupyter Widget property change, the event to listen for would be `change:<property-name>`.
- `useModel` is an escape hatch that gives you full access to the Jupyter `WidgetModel`.

## Releasing your initial packages:

- Add tests
- Ensure tests pass locally and on CI. Check that the coverage is reasonable.
- Make a release commit, where you remove the `, 'dev'` entry in `_version.py`.
- Update the version in `package.json`
- Relase the npm packages:
  ```bash
  npm login
  npm publish
  ```
- Bundle the python package: `python setup.py sdist bdist_wheel`
- Publish the package to PyPI:
  ```bash
  pip install twine
  twine upload dist/<python package name>*
  ```
- Tag the release commit (`git tag <python package version identifier>`)
- Update the version in `_version.py`, and put it back to dev (e.g. 0.1.0 -> 0.2.0.dev).
  Update the versions of the npm packages (without publishing).
- Commit the changes.
- `git push` and `git push --tags`.
