
## How do I edit a page?

Just open the file in Github and hit Preview. Then use the form below to commit. Please put in 
a decent commit message or we'll hit you with the clue-bat.


## But I want to develop this locally

Okay, you'll have to install Jekyll, then run the Jekyll server.

### Installing Jekyll

```bash
# Install `rvm`:
curl -sSL https://get.rvm.io | bash

# Install the ruby binary that Github uses:
rvm install 2.1.0  # this will ask for sudo to install dependencies through apt
rvm use 2.1.0

# Create and use a gemset
rvm gemset create hypernode
rvm gemset use hypernode

# Install the github-pages software
gem install github-pages
```

### Running the server

```bash
jekyll serve --watch
```

* Now look at http://localhost:4000/.
* If you edit a page and save, Jekyll will regenerate the HTML.
* If you edit the configuration files, you will have to restart Jekyll.
