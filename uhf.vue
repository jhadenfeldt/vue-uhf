<script>
	import axios from 'axios';
	import JQuery from 'jquery';
	window.$ = window.jQuery = JQuery;

	export default {
		props: {
			partnerID: {
				type: String,
				default: 'UHFPortal'
			},
			headerID: {
				type: String,
				default: 'UHFPortalHeader'
			},
			footerID: {
				type: String,
				default: 'UHFPortalFooter'
			},
			cookieCompliance: {
				type: Boolean,
				default: false
			}
		},
		render() {
			return this.$slots.default;
		},
		created() {
			axios.get(`https://uhf.microsoft.com/en-us/shell/xml/${this.partnerID}?headerid=${this.headerID}&footerid=${this.footerID}&CookieComplianceEnabled=${this.cookieCompliance}`)
				.then(response => {
					let xmlParser = new DOMParser();
					let xmlData = xmlParser.parseFromString(response.data, 'application/xml');
					let cssIncludes = xmlData.querySelector('cssIncludes').textContent;
					let javascriptIncludes = xmlData.querySelector('javascriptIncludes').textContent;
					let headerHtml = xmlData.querySelector('headerHtml').textContent;
					let footerHtml = xmlData.querySelector('footerHtml').textContent;

					// Add CSS
					document.querySelector('head').insertAdjacentHTML('beforeend', cssIncludes);

					// Add header and footer
					document.querySelector('body').insertAdjacentHTML('afterbegin', headerHtml);
					document.querySelector('body').insertAdjacentHTML('beforeend', footerHtml);

					// Cache header and footer
					this.uhfElements = document.querySelectorAll('.uhf');

					// Hide header and footer
					this.uhfElements[0].style.display = 'none';
					this.uhfElements[1].style.display = 'none';

					this.uhfElements[0].classList.add('vue-uhf-added');
					this.uhfElements[1].classList.add('vue-uhf-added');

					setTimeout(() => {
						// Show header and footer
						this.uhfElements[0].style.display = 'block';
						this.uhfElements[1].style.display = 'block';

						// Add JS
						let range = document.createRange();
						range.setStart(document.querySelector('body'), 0);
						document.querySelector('body').appendChild(range.createContextualFragment(javascriptIncludes));
					}, 500);
				});
		},
		beforeDestroy() {
			this.uhfElements[0].remove();
			this.uhfElements[1].remove();
		}
	}
</script>
