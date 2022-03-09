# Atlassian Marketplace Release Action

This repo defines composite actions to build and release server app to Atlassian marketplace.

## verify-release-tag
Verify that the release is tagged on a certain branch.
<p>This action will fail if the tag is not contained in the specified branch. It to make sure the release process is only triggered on a certain branch.
<p>The tag is retrieved from `github.event.release.tag_name`

### Inputs
|Name|Required|Description|
|---|---|---|
| `branch` | Yes | The release branch to checkout |
| `githubToken` | Yes | The GitHub PTA to checkout the repo |

### Outputs
|Name|Description|
|---|---|
| `release-version` | The release version from git tag |

## setup-node
Setup node environment if the `package.json` is available at the root of checked out repository.
<p>This action installs `npm` and `node` using engine information of `package.json`. If `package.json` is available but neither `node` nor `npm` is specified then it will fail.

### Inputs
None

### Outputs
|Name|Description|
|---|---|
| `is-node-project` | `true` if the current repository is node project, `false` otherwise |
| `node-version` | The installed `node` version |
| `npm-version` | The installed `npm` version |

## build-jar
Build server app into jar files using `atlas-mvn package` command and upload the result as assets to GitHub release.

### Inputs
|Name|Required|Description|
|---|---|---|
| `release-version` | Yes | The new version to bump |
| `branch` | Yes | The release branch to build |
| `githubToken` | Yes | The GitHub PTA to upload the jar files as assets to GitHub release |

### Outputs
|Name|Description|
|---|---|
| `addon-key` | The app addon-key extracted from `atlassian-plugin.xml` |
| `should-bump-version` | `true` if the `release-version`:<br /> <ul><li>is different from the one specified in `pom.xml`AND</li><li>is tagged at head of release branch</li></ul>`false` otherwise. |
| `file-path` | The relative path to the jar file |
| `file-name` | The jar file name in format `artifactId`.jar |

## marketplace
Release the app to Atlassian marketplace and bump the required version for server to cloud migration.
<p>This action makes a new release for an existing app. It will fail if the app does not exist.

### Inputs
|Name|Required|Description|
|---|---|---|
| `addonKey` | Yes | The app addon-key |
| `filePath` | Yes | Path to the jar file |
| `fileName` | Yes | The name of the jar file |
| `version` | Yes | The new version to release |
| `marketplaceUser` | Yes | The Atlassian marketplace user |
| `marketplaceToken` | Yes | The Atlassian marketplace token |

### Outputs
None

## release
Combine 4 actions above to perform full release process:
* verifying release tag 
* setup environment
* build and upload jar file to GitHub
* release to marketplace
* make change to `pom.xml` file if should bump new version

### Inputs
|Name|Required|Description|
|---|---|---|
| `addonKey` | Yes | The app addon-key |
| `branch` | Yes | The release branch to checkout |
| `githubUserName` | Yes | Github username to commit |
| `githubUserEmail` | Yes | Github user email to commit |
| `githubToken` | Yes | The GitHub PTA to checkout the repo |
| `marketplaceUser` | Yes | The Atlassian marketplace user |
| `marketplaceToken` | Yes | The Atlassian marketplace token |

### Outputs
None