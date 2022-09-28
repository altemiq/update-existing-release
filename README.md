# update-existing-release

Create, update, and edit existing releases!

Great for creating a `latest` or `nightly` release.

```yaml
- uses: IsaacShelton/update-existing-release@v1.3.1
  with:
    token: ${{secrets.GITHUB_TOKEN}}
    release: "Nightly"
    tag: "nightly"
    replace: true
    files: >
      stage/MacOS-arm64-Installer-nightly.pkg
      stage/Windows-x86_64-Installer-nightly.exe
      stage/Ubuntu-x86_64-Standalone-nightly.zip
```


### Fork History

This action is the successor to the `update-release` GitHub action. It has been updated to work with the latest GitHub APIs and also includes bugs fixes and additional features.

[`IsaacShelton/update-existing-release`](https://github.com/IsaacShelton/update-existing-release) <- [`ColinPitrat/update-release`](https://github.com/ColinPitrat/update-release) <- [`johnwbyrd/update-release`](https://github.com/johnwbyrd/update-release)


### Inputs
This action has the following inputs:

| Input        | Description                                                             |
| ------------ | ----------------------------------------------------------------------- |
| `token` **(required)**     | This should be [your secure Github token](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/authenticating-with-the-github_token). Use `${{ secrets.GITHUB_TOKEN }}` if your build lasts less than an hour. Otherwise, you will need to [create your own access token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) with repo admin access, [store it as a secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) in your own repository, and reference that secret token in your build. |
| `files` **(required)**      | The files that you want for your the release. Presumably, this should include at least one file that you just built. File paths can be provided as absolute paths, or they can be relative to `${{ github.workspace }}`. |
| `release`       | The name of the release to be created. If none is supplied, a reasonable looking one will be chosen based on `${{ github.ref }}`. |
| `tag`       | The name of the tag to be created. For some inexplicable reason, Github thinks that you need to have a tag corresponding to every release, which makes no sense if you're using Github to do continuous integration builds.  The tag will be the same as the calculated name of the release, if this input is not supplied.  |
| `message`       | A brief description of the tag and also of the release.  |
| `body`       | A longer description of the release, if it is created.  |
| `prerelease`       | Whether to mark the release, if created, as a prerelease. Such releases are generally publicly visible. This is `false` by default. |
| `draft`       | Whether to mark the release, if created, as a draft. Such releases are generally not publicly visible. This is `false` by default. |
| `replace ` (since v1.1.0)       | Whether to remove existing files of the release that are not overwritten. This will cause all existing files attached to the release to be removed and replaced with the files provided. This is `false` by default. |
| `updateTag ` (since v1.2.0)       | Whether to update the tag to the latest commit. If the tag doesn't already exist, it will be created pointing to the latest commit regardless of this option. This is `true` by default. |

### Outputs

If successful, this action will have the following outputs:

| Output       | Description                                                             |
| ------------ | ----------------------------------------------------------------------- |
| `files`      | The calculated local paths of the files to be uploaded into the release |
| `draft`      | Whether the release, if created, was marked as a draft                  |
| `prerelease` | Whether the release, if created, was marked as a prerelease             |
| `release`    | The name of the release                                                 |
| `tag`        | The tag used to create the release                                      |


### For builds lasting more than an hour

The `${{ secrets.GITHUB_TOKEN }}` is valid for exactly an hour from the time your build starts.  If your build requires longer than an hour to run, you will need to [create your own access token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) with repo admin access, [store it as a secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) in your own repository, and reference that secret token in your build:

    - uses: IsaacShelton/update-existing-release@v1.3.1
      with:
        token: ${{ secrets.YOUR_PRIVATE_SECRET_TOKEN }}
        release: Nightly
        replace: true
        files: >
          stage/x86_64-Windows-HelloWorld.exe
          stage/arm64-MacOS-HelloWorld
          stage/x86_64-Ubuntu-HelloWorld



### Overall Security Precautions

- Review the source code all Github actions you use (including this one)
- Verify that they don't store, transmit, or otherwise mistreat your secure Github token
- With your secure access token comes the permission to control to your repository

*For extra peace of mind, feel free to review and then rebuild this action using `npm run bundle` to make sure that the minified `dist/main.js` corresponds exactly to the one in the repo.*

*This action also has the additional security feature of helpfully marking the token that you provide it as a secret so that it doesn't inadvertently sneak into any log files.*

### Changelog

**`v1.3.1`**

- Fixed bug that caused releases with more than 100 assets to not be updated correctly
- Thank you to our contributors:
	- beyarkay

**`v1.3.0`**

- Validation of tag name choice
- Fixed issue with first time creating a release


**`v1.2.0`**

- Added `updateTag` option to automatically update the tag to the latest commit

**`v1.1.0`**

- Updated all dependencies to latest versions
- Code now works with latest version of GitHub API
- Added `replace` option, to allow for removing attached files that aren't overwritten
- Now works correctly when the release doesn't exist already (it will be added before updating)
- Cleaned up a little of the code, although it still isn't the cleanest

**`v1.0.1`**

- Stale repo [ColinPitrat/update-release](https://github.com/ColinPitrat/update-release) 

**`v1.0.0`**

- Stale repo [johnwbyrd/update-release](https://github.com/johnwbyrd/update-release) 


### Building it yourself

**Setup:**

To build under Debian (can be adopted for other platforms):

```
apt-get install webpack npm
npm install --save-dev typescript ts-loader v8-compile-cache
npm run bundle
```

**Details:**

This action uses `Node.js`, `TypeScript`, `webpack`, and `ESLint`.

Use `npm install` to install all `package.json` dependencies of `update-existing-release` before hacking on it.

Several npm run targets were added for ease of development:

- `test` builds readable `dist/main.js` and `dist/main.map.js` files for source-level debugging
- `test-watch` watches `src/main.ts` for changes and lints/recompiles as needed
- `bundle`  prepares a production ready `dist/main.js`

**Debugging:**

This action uses `.env` files in order to help facilitate debugging.  It will read a `.env` file, if it exists, as the root of the installation, and uses it to populate the environment variables for local testing. A typical `.env` file for testing this action might look something like this:

    INPUT_ASSET=your-build-asset.zip
    INPUT_TOKEN=00000000000000000000000000000001
    GITHUB_REPOSITORY=your-username/your-repo
    GITHUB_REF=refs/heads/master
    GITHUB_WORKSPACE=/absolute/local/path/to/workspace

Using an `.env` file can allow you to perform local testing and debugging without having to build a product first.


### Contributions

All pull requests are welcome and will be reviewed promptly.


### Thank you

Contributors will have there names listed below:

- johnwbyrd
- ColinPitrat
- IsaacShelton
- beyarkay