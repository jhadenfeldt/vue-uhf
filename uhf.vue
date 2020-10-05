<script>
import axios from "axios";

export default {
    props: {
        // UHF options
        partnerId: {
            type: String,
            default: "UHFPortal",
        },
        headerId: {
            type: String,
            default: "UHFPortalHeader",
        },
        footerId: {
            type: String,
            default: "UHFPortalFooter",
        },
        cookieCompliance: {
            type: Boolean,
            default: false,
        },
        trackingId: {
            type: String,
            default: "",
        },

        // Loading options
        headerContainerId: {
            type: String,
            default: null,
        },
        footerContainerId: {
            type: String,
            default: null,
        },
        includeJQuery: {
            type: Boolean,
            default: true,
        },
    },
    data() {
        return {
            uhfElements: [], // all DOM elements to be removed on beforeDestroy
        };
    },
    mounted() {
        const { uhfElements } = this;
        const body = document.body;

        Promise.all([
            axios.get(
                `https://uhf.microsoft.com/en-us/shell/xml/${this.partnerId}?headerid=${this.headerId}&footerid=${this.footerId}&CookieComplianceEnabled=${this.cookieCompliance}&Tracking=${this.trackingId}`
            ),
            this.includeJQuery
                ? this.loadingScriptByUrl("https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js")
                : null,
        ])
            .then((responses) => {
                // jQuery and UHF data loaded:

                const xmlParser = new DOMParser();
                const xmlData = xmlParser.parseFromString(responses[0].data, "application/xml");

                // Load stylesheets

                const cssIncludes = xmlData.querySelector("cssIncludes").textContent;
                const divStyles = document.createElement("div");
                divStyles.classList.add("uhf-includes");
                divStyles.innerHTML = cssIncludes;
                uhfElements.push(divStyles);

                const promises = Promise.all([
                    Promise.resolve(xmlData),
                    ...[...divStyles.querySelectorAll("link")].map((el) => this.loading(el)),
                ]);

                body.appendChild(divStyles);

                return promises;
            })
            .then(([xmlData]) => {
                // Stylesheets loaded:
                // Add header/footer markup to page

                const headerHtml = xmlData.querySelector("headerHtml").textContent;
                const footerHtml = xmlData.querySelector("footerHtml").textContent;
                const headerContainer = document.getElementById(this.headerContainerId || "") || body;
                const footerContainer = document.getElementById(this.footerContainerId || "") || body;

                headerContainer.insertAdjacentHTML("afterbegin", headerHtml);
                footerContainer.insertAdjacentHTML("beforeend", footerHtml);

                uhfElements.push(...document.querySelectorAll(".uhf"));

                // Load JavaScript

                const jsIncludes = xmlData.querySelector("javascriptIncludes").textContent;
                const divScripts = document.createElement("div");
                divScripts.classList.add("uhf-includes");

                const range = document.createRange();
                range.setStart(divScripts, 0);
                divScripts.appendChild(range.createContextualFragment(jsIncludes));
                uhfElements.push(divScripts);

                const promises = Promise.all(
                    [...divScripts.querySelectorAll("script")].map((el) => this.loading(el))
                );

                body.appendChild(divScripts);

                return promises;
            })
            .then(() => {
                // JavaScript loaded:

                this.$emit("ready");
            })
            .catch((error) => {
                this.$emit("error", error);
            });
    },
    beforeDestroy() {
        this.uhfElements.forEach((el) => {
            el.remove();
        });

        this.uhfElements = [];
    },
    methods: {
        loading(el) {
            return new Promise((resolve, reject) => {
                el.onload = function () {
                    resolve(el);
                };

                el.onerror = function () {
                    reject(new Error(`Failed to load ${el}`));
                };
            });
        },
        loadingScriptByUrl(url) {
            return new Promise((resolve, reject) => {
                const element = document.createElement("script");
                const parent = "body";
                const attr = "src";

                element.onload = function () {
                    resolve(url);
                };

                element.onerror = function () {
                    reject(url);
                };

                element.async = true;
                element[attr] = url;
                this.uhfElements.push(element);
                document[parent].appendChild(element);
            });
        },
    },
    render() {
        return this.$slots.default;
    },
};
</script>
