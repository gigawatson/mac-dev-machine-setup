# Dev Machine Setup (Mac)

This is more or less in order if setting up a new dev machine. If not, prerequisites should be obvious.

## Fonts

[Fira Code](https://github.com/tonsky/FiraCode) is a nice monospace font with programming ligatures for use in all consoles and code editors.

## Code Editor

Install [VS Code](https://code.visualstudio.com/) with the following settings:

→ [`settings.json`](https://github.com/gigawatson/dev-machine/blob/main/vscode-settings.json)

### Extensions

- One Dark Pro theme
- Material Icon theme
- PHP Intelephense
- Alpine.js IntelliSense
- Tailwind CSS IntelliSense
- DotENV
- ESLint

## Terminal

Install [iTerm2](https://iterm2.com/index.html) for a better Shell experience with the following settings:

- **Minimal** theme.
- **Dark Background** color preset.
- Font set to **FiraCode / Light / 16pt / 146 line height**.
- `` ⌃` `` Hotkey.

Remove the "last login time" message in the terminal window by creating a `.hushlogin` file in the root directory.

```zsh
touch .hushlogin
```

Install [Oh My ZSH](https://ohmyz.sh) to move from Bash to a more interactive Zsh.

## Package Management (Homebrew, Composer, npm)

Install [Homebrew](https://brew.sh) for general system package management with the following packages:

```zsh
brew install php mysql git node
```

Install [Composer](https://getcomposer.org) for PHP dependency management. Once installed, make it available globally with:

```zsh
mv composer.phar /usr/local/bin/composer
```

Add the Composer vendor bin directory to `$PATH` using:

```zsh
sudo vim /etc/paths
```

Then append `/Users/{username}/.composer/vendor/bin` to the file.

## Laravel (and Valet)

Install the [Laravel Installer](https://laravel.com) globally for PHP projects as well as Laravel Valet for a fast and minimal dev environment using Nginx and DnsMasq:

```zsh
# Install Laravel Installer
composer global require laravel/installer

# Install Laravel Valet
composer global require laravel/valet
```

Create a **project code directory** (`~/code/sites`) and register it with Valet so top directories automatically get their own `*.test` domain.

```zsh
mkdir -p code/sites && cd code/sites
valet install
valet park
```

## Database Management

Use [TablePlus](https://tableplus.com/).

### phpMyAdmin (optional)

Install phpMyAdmin via Homebrew.

```zsh
brew install phpmyadmin
```

Configure phpMyAdmin by opening `/usr/local/etc/phpmyadmin.config.inc.php` and adjusting the following settings:

-   Set `$cfg['Servers'][$i]['AllowNoPassword']` to `true`.
-   Add a 32 char `$cfg['blowfish_secret']`.

Link phpMyAdmin with Valet (for https://pma.test):

```zsh
cd /usr/local/share/phpmyadmin
valet link pma
valet secure pma
```

## Git (and GitHub)

Install [GitHub Desktop](https://desktop.github.com), sign-in to GitHub, and clone projects into the `~/code` directory or the `~/code/sites` directory (for things you want automatially set up as a `.test` Laravel Valet site).

Add a global `.gitignore` in the root directory and tell git about the file:

```zsh
touch .gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

Add these files and directories to the `.gitignore_global`:

- `.DS_Store`
- `.vscode`
- `*.log`
- `*.sql`

### SSH (and GPG)

For authentication, generate a new SSH key with a blank passphrase (using [GitHub's instructions](https://github.com/settings/keys)). Add the key to both the ssh-agent and to GitHub. Don't forget to back up the secret key.

For commit signing, install [GPG Suite](https://gpgtools.org) (without GPG Mail) and create a new key pair (or import one from another computer). If new, add the key to GitHub. Don't forget to backup the secret key and passphrase.

Tell Git to use GPG and the new signing key (globally) with:

```zsh
git config --global commit.gpgsign true
git config --global user.signingkey {SIGNING_KEY}
```

## Bundlers

[Webpack](https://webpack.js.org/) is nice if you're using [Laravel Mix](https://laravel-mix.com/). Otherwise, you should probably stop wasting your life away and just download [CodeKit](https://codekitapp.com/).

## CMS

### WordPress

Install a dev installation of WordPress using Laravel Valet and wp-cli:

```zsh
# Install wp-cli
brew install wp-cli

# Create the new directory
cd code && mkdir wp && cd wp

# Download the latest WP version
wp core download

# Start the SQL server
mysql.server start

# Create a WP config file with debug constants set
wp config create --dbname=wptest --dbuser=root --extra-php <<PHP
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
PHP

# Create the database
wp db create

# Use HTTPS
valet secure wp

# Install WP (adjust details)
wp core install --url=https://wp.test --title="WP Dev Playground" --admin_user=user --admin_password=pass --admin_email=user@email.com --skip-email

# Set permalinks
wp rewrite structure '/%postname%/' --category-base=topics

# Delete the sample posts
wp post delete 1 2 --force

# Delete the default plugins
wp plugin delete hello akismet

# Install and activate dev plugins
wp plugin install query-monitor user-switching --activate
```

### Statamic

Maybe stop living in the past with WordPress and upgrade to something [rad](https://statamic.com/).

Install the [Statamic CLI](https://statamic.dev/installation) globally.

```zsh
composer global require statamic/cli
```

Then create a Statamic site.

```zsh
# Create a new Statamic site
cd code
statamic new {site_name}

# Create the first user
php please make:user

# Use HTTPS
valet secure
```

Here's a nice Laravel Mix (webpack) starter config so it works nicely with [Browsersync](https://browsersync.io/) and a secured (https) `.test` site with Laravel Valet.

→ [`webpack.mix.js`](https://gist.github.com/gigawatson/99aa9308ff57d677a3f68c01902677bd)

## Simple PHP Site Setup

A simple PHP site that uses Laravel Valet, Laravel Mix, AlpineJS, and Tailwind CSS.

→ [`1-simple-php-site.md`](https://gist.github.com/gigawatson/f7a55520400e898e21a03f250e1cbae2)

---

**Now go make stuff!**
