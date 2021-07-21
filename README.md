# #撻著一句廣東歌歌詞 2021 海报生成器

<!-- This is a poster generator for ApacheCon Asia 2021. [[Run]](http://zhangwenli.com/acasia-poster/) -->

撻著廣東歌你可我可,自由DIY 
<!-- [[Run]](http://zhangwenli.com/acasia-poster/) -->

It provides a form to input the name, title and other information so that each speaker of the event can generate their own poster easily with auto-completion.

## Setup

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project to `docs`
$ npm run generate
```

## Adapt To Your Own Events

1. Change `router.base` in `nuxt.config.js` to be the base of your server path
2. Change the form in `pages/index.vue`.
3. If you need auto-completion of the form, change `pages/data` and the logic with `trackZh` and `trackEn` in `pages/index.vue`.
4. If you are looking for a tool to generate QR code with different reference information in the URL, please check out [Ovilia/qr-baker](https://github.com/Ovilia/qr-baker/blob/main/index.js).

## Framework

This repo uses [NuxtJS](https://nuxtjs.org/).

For detailed explanation on how things work, check out the [documentation](https://nuxtjs.org).

### Special Directories

You can create the following extra directories, some of which have special behaviors. Only `pages` is required; you can delete them if you don't want to use their functionality.

#### `assets`

The assets directory contains your uncompiled assets such as Stylus or Sass files, images, or fonts.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/assets).

#### `components`

The components directory contains your Vue.js components. Components make up the different parts of your page and can be reused and imported into your pages, layouts and even other components.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/components).

#### `layouts`

Layouts are a great help when you want to change the look and feel of your Nuxt app, whether you want to include a sidebar or have distinct layouts for mobile and desktop.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/layouts).


#### `pages`

This directory contains your application views and routes. Nuxt will read all the `*.vue` files inside this directory and setup Vue Router automatically.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/get-started/routing).

#### `plugins`

The plugins directory contains JavaScript plugins that you want to run before instantiating the root Vue.js Application. This is the place to add Vue plugins and to inject functions or constants. Every time you need to use `Vue.use()`, you should create a file in `plugins/` and add its path to plugins in `nuxt.config.js`.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/plugins).

#### `static`

This directory contains your static files. Each file inside this directory is mapped to `/`.

Example: `/static/robots.txt` is mapped as `/robots.txt`.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/static).

#### `store`

This directory contains your Vuex store files. Creating a file in this directory automatically activates Vuex.

More information about the usage of this directory in [the documentation](https://nuxtjs.org/docs/2.x/directory-structure/store).
