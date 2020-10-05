# vue-uhf
> A Vue.js component for dynamically loading the Microsoft UHF

# Usage
## Install the component

``npm i -s vue-uhf``

## Import the component

```html
<template>
    <div class="page">
        <UHF partnerID="YourPartnerID" headerID="YourHeaderID" footerID="YourFooterID" :cookieCompliance="true" />
    </div>
</template>

<script>
import UHF from 'vue-uhf';

export default {
    components: {
        UHF
    }
};
</script>
```

## Configure "Skip to main content" link

Microsoft accessibility standards require that keyboard users be able to skip over header and navigation elements and go straight to the main area of any page. By default the main content ID is set to `mainContent`.

```html
<div id="mainContent">
    <!-- App content here -->
</div>
```

## Configure header/footer placement

By default the header will be prepended and the footer will be appended to the `<body>`-tag. Set `headerContainerId` and `footerContainerId` to place the markup within specific containers of your layout.

Suggested layout:

```html
<!-- App.vue -->

<template>
    <div>
        <UHF header-container-id="headerContent" footer-container-id="footerContent" />
        <header id="headerContent"></header>
        <main id="mainContent">
            <router-view />
        </main>
        <footer id="footerContent"></footer>
    </div>
</template>
```

[Layout example](https://gist.github.com/sonjastrieder/c9ea2f184bcf273cb4fa91440c36be8d)

## Browser support

If support for legacy browsers like IE11 is required please ensure to explicitly transpile this dependency with Babel. See [transpileDependencies](https://cli.vuejs.org/config/#transpiledependencies)

# Changelog

## 1.0.4
- update jQuery due to vulnerability (see: https://nvd.nist.gov/vuln/detail/CVE-2019-11358)

## 1.0.3
- update license

## 1.0.1
- fix duplicate headers and footers when switching between routes

## 1.0.0
- Initial commit
