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

# Changelog

## 1.0.4
- update jQuery due to vulnerability (see: https://nvd.nist.gov/vuln/detail/CVE-2019-11358)

## 1.0.3
- update license

## 1.0.1
- fix duplicate headers and footers when switching between routes

## 1.0.0
- Initial commit
