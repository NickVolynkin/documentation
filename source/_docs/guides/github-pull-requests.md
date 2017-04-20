---
title: Using GitHub Pull Requests with Composer on Pantheon
description: Use GitHub and Composer to manage modules and other dependencies for Drupal 8 sites on Pantheon.
tags: [workflow]
categories: [drupal8]
contributors:
  - greg-1-anderson
  - stevector
---

This guide describes how to use GitHub and Circle CI with Composer to implement a collaborative team-based Continuous Integration workflow using pull requests on Pantheon.

<div class="row">
  <div style="margin-bottom:30px;" class="col-md-4">
    <div class="plugin-dir">
      <div class="pantheon-official">
        <div class="main-topic-info__plugin-image" style="background-image:url(/source/docs/assets/images/github-logo.svg)"></div>
        <p>GitHub</p>
      </div>
      <div class="terminus-plugin">
        <h3 class="plugin-title">GitHub</h3>
          <p class="topic-info__description"><a href="https://github.org">GitHub</a> is an online service that provides cloud storage Git repositories that may be cloned and used locally or edited directly through their web-based management interface. These features are very useful to teams collaborating on a project together.</p>
      </div>
    </div>
  </div>
  <div style="margin-bottom:30px;" class="col-md-4">
    <div class="plugin-dir">
      <div class="pantheon-official">
        <div class="main-topic-info__plugin-image" style="background-image:url(/source/docs/assets/images/circleci-logo.svg)"></div>
        <p>Circle CI</p>
      </div>
      <div class="terminus-plugin">
        <h3 class="plugin-title">Circle CI</h3>
        <p class="topic-info__description"><a href="https://circleci.com">Circle CI</a> provides hosted services to run automated tests for a project, and GitHub provides an integration to run these tests to whenever a change is submitted. The process of testing each set of changed files prior to merging them into the main branch is called continuous integration.</p>
      </div>
    </div>
  </div>
  <div style="margin-bottom:30px;" class="col-md-4">
  <div class="plugin-dir">
      <div class="pantheon-official">
        <div class="main-topic-info__plugin-image" style="background-image:url(/source/docs/assets/images/composer-logo.svg)"></div>
        <p>Composer</p>
      </div>
      <div class="terminus-plugin">
          <h3 class="plugin-title">Composer</h3>
          <p class="topic-info__description"><a href="https://getcomposer.org">Composer</a> is a PHP dependency manager that provides an alternative, more modern way to manage the external code used by a project. For example, Composer may be used to install the modules and themes used by a Drupal site.</p>
      </div>
    </div>
  </div>
</div>

Pull requests are a formalized way of reviewing and merging a proposed set of changes to a codebase. When one member of a development team makes changes to a project, all of the files modified to produce the feature are committed to a separate branch, and that branch becomes the basis for the pull request. GitHub allows other team members to review all of the differences between the new files and their original versions, before the pull request. It is also common to set up automated tests to confirm that the project is working as expected; when tests are available, GitHub will run them and display the results of the tests with the pull request. Working on projects with comprehensive tests increases the development team's confidence that submitted pull requests will work correctly when they are integrated into the main build.

When using GitHub and Composer to manage a Drupal site, only those files unique to the project are part of the project's main repository. Composer is used to fetch the external code needed by the project; a process running on Circle CI executes Composer, and ensures that the final composed build results are installed on Pantheon.

![Artifact Deployment](/source/docs/assets/images/artifact-deployment.png)

One advantage of managing code this way is that it keeps the change sets (differences) for pull requests as small as possible. If a pull request upgrades several external projects, only the external dependency metadata file will change; the actual code changes in the upgraded projects themselves are not shown.

In some cases, use of Composer is optional; however, some Drupal modules, such as the Address module require the use of Composer. If a site needs just one module that requires Composer, then it should manage all of its modules with Composer.

## Setting Up a New Project

Setting up multiple distributed services can be complicated, and, at the moment, this can only be done from the command line. Fortunately, the Terminus Build Tools Plugin makes this setup relatively simple. To use it:

- Install [Composer](https://getcomposer.org).
- Install [Terminus](https://github.com/pantheon-systems/terminus#installation).
- Install the [Terminus Build Tools Plugin](https://github.com/pantheon-systems/terminus-build-tools-plugin#installation).
- Generate a [machine token](https://pantheon.io/docs/machine-tokens/) and [log in with Terminus](https://pantheon.io/docs/terminus/install/#authenticate).

Then, run the command:
```
terminus build-env:create-project my-pantheon-site
```
Select a suitable descriptive name to use in place of "my-pantheon-site". The create-project command will prompt for any additional information it may need to set up the build workflow. The required information needed includes:

- GitHub [personal access token](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).
- Circle CI [personal API token](https://circleci.com/account/api).
- Password for the CMS admin account.
- The Pantheon team the site should be associated with (recommended).

It is possible to avoid prompting by providing the necessary information either via [environment variables](https://github.com/pantheon-systems/terminus-build-tools-plugin#credentials) or command line options. Run `terminus help build-env:create-project`, or see the [Terminus Build Tools Plugin project page](https://github.com/pantheon-systems/terminus-build-tools-plugin) for more information.

Once you have provided the required information, the rest of the process is automatic. Once your site is ready, the URL to your project page will be printed to your terminal window. Copy this address and paste it into a browser to visit your new project.

## Your Site's Project Page

Your starting project page contains badges that link to:

- The Circle CI page for your project
- Your Pantheon dashboard
- Your test site

Click on these badges to quickly navigate to the different components used to manage your site. If you click on the Circle CI badge, you can watch you project's initial test run.

## Create a Pull Request

When using the Composer pull request workflow, you should never modify your dev environment. Always begin by creating a new pull request to work in. This can be done easily from GitHub, as described below. Presently, there is no way to create a pull request from the Pantheon dashboard.

From your GitHub project page, click on the `config` directory. Find the file named `system.site.yml`, click on it, and use the edit pencil to open an editor.

Change the `slogan` to something inspiring like `Synergizing Cross-Functional Competencies to Iteratively Maximize Customer Value`.

Describe your change in the "Commit Changes" area.

Click the radio button to create a new branch.

Give the branch a short name, like `slogan`.

Click on `Commit Changes`.

On the pull request page, click on `Create Pull Request`.

At this point, Circle CI will build a new multidev environment and install a site that you can use to preview the change.

## Your Project's Behat Tests

There are tests provided for your site. You can customize these or add more to suit your purposes.

To confirm that your site's configuration has been applied to the test site, you could add a test to check that the site slogan is correct.

## Configuring Your Site Through Drupal's Admin Interface

While it is possible to configure your site by editing the exported configuration files, it will usually be more convenient to use the Drupal Admin interface to build your site.

Log in to your `pr-slogan` multidev site.

Go to `Structure` -> `Blocks`.

Disable the `Tools` block and move the `Search` block to the header.

Save your changes.

Go to `Configuration` -> `Development` -> `Configuration Synchronization`. Note the warning about modified configuration. This means that your recent configuration changes would be erased if you synchronized your configuration at this time.

Instead, click on the `Update` tab, select the `sync` source and click `Update Configuration`. This tab is provided by the `config_direct_save` contrib module, which is added by the example starting project by default. Updating your configuration writes the active configuration from the database into the configuration files on disk.

Visit your site's Pantheon dashboard. Go to the `pr-slogan` multidev page. Note that there are a handful of modified files.

Type in a brief description of what you changed and click `Commit`.

Once the Pantheon dashboard finishes committing the code, visit your project page on GitHub. Go to your `slogan` pull request. Note that your commit has been added to this pull request, and the Circle CI status indicates that your tests are running. Click on `details` and check on the status of your new build.

Note that the build process will overwrite the code on your existing multidev site. You may continue to make multiple changes in the same pull request, and may even continue working on the test site for a PR while tests are running, but avoid changing code (e.g. updating configuration) while a build is in process.

## Add a New Module

When using the Composer workflow, you should never use the Drupal `Extend` -> `Install new module` feature, nor should you use `drush pm-download`. Instead, Composer will be used to add all external code.

Run:
```
terminus composer site.env -- require drupal/path_auto
```
Visit your test site and use `Extend` to enable your module, or use Drush to do it.
```
terminus drush site.env -- pm-enable path_auto --yes
```
Export your configuration as shown above, or do it with Drush.
```
terminus drush site.env -- config-export --yes
```
Visit your Pantheon dashboard and commit your changes.

## Update your Project

When using the Composer workflow, you should never use the Pantheon dashboard to update changes from your upstream, nor should you ever merge code from one environment to another.  All code updates will be done using Composer.

Using your existing pull request.

Run:
```
terminus composer site.env -- update
```
Visit your Pantheon dashboard and commit your changes.

## Create a Custom Theme

Most Drupal sites will have a custom theme. This is how the appearance of a Drupal site is customized.

Use Drupal Console to create a new theme.
```
terminus drupal site.env -- generate:theme
```
Answer a bunch of questions.

Open up a generated .css file using SFTP mode.

Make a minor change.
```
terminus drush site.env -- cache-rebuild
```
See the change on your test site.

Visit your Pantheon dashboard and commit your change.

Maybe link to another page on LESS / SASS here?

## Merge your Pull Request

Go to your GitHub project page. Visit your open pull request.

Note that your tests have completed, and the test results are green.

Click on `Merge`.

When your pull request is merged, your PR multidev environment will be merged into your development environment. Note that database content is not merged; make sure that you have exported your configuration before merging your pull request to ensure that configuration changes are not lost.

Once the merge to the development environment is done, your pull request multidev environment is automatically deleted.

At this point, you may use the Pantheon dev / test / live workflow to deploy your site as usual.
