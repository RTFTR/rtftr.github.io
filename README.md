<!-- You shouldn't need to edit this file -->
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<title></title>
		<link id="favicon" rel="shortcut icon" type="image/png" />
		<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
		<meta name="description"/>
		<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0" />
		<!-- Fonts -->
		<link rel="preconnect" href="https://fonts.googleapis.com" />
		<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
		<link
			href="https://fonts.googleapis.com/css2?&family=Roboto+Mono&family=Roboto:wght@400;600&family=Rubik:wght@400;600&display=swap"
			rel="stylesheet"
		/>
		<!-- Themes -->
		<link rel="stylesheet" href="https://unpkg.com/docsify-themeable/dist/css/theme-simple-dark.css" />
		<link rel="stylesheet" href="assets/codeTheme.css" />
		<link rel="stylesheet" href="assets/customTheme.css" />
		<!-- Config -->
		<script type="text/javascript" src="config.js"></script>
	</head>
	<body>
		<div id="app"></div>
		<script>
			// SET TITLE
			document.title = config.name + ' Documentation';

			// SET META DESCRIPTION
			document.querySelector('meta[name="description"]').setAttribute('content', config.description);

			/* DOCSIFY CONFIG */
			window.$docsify = {
				name:
					'<a id="home-link" class="app-name-link" data-nosearch href="/"><span id="home-text" title="' +
					config.description + '">' + config.name +
					' Docs</span><br></a>' +
					'<select id="version-selector" name="version">' +
					'</select>',
				nameLink: false,
				loadSidebar: '_sidebar.md',
				loadNavbar: true,
				//basePath: '/Augury-Docs/',
				subMaxLevel: 3,
				relativePath: true,
				auto2top: true,
				footer: {
					pre: '<br><hr>',
					copy: '<span>' + config.name + ' &copy; ' + new Date().getFullYear() + '</span>',
					auth:
						'Generated with <a href="https://github.com/chesrowe/Tome" target="_blank">Tome</a>' +
						' and powered by <a href="https://github.com/docsifyjs/docsify/" target="_blank">Docsify</a>',
					style: 'text-align: right;',
				},
				tabs: {
					persist: false,
					sync: false,
					theme: 'classic',
					tabComments: false,
					tabHeadings: true,
				},
				copyCode: {
					buttonText: '📋',
				},
				search: {
					paths: 'auto',
					namespace: config.name.toLowerCase(),
					hideOtherSidebarContent: true,
					depth: 3,
					pathNamespaces: (() => {
						let pathNamespacesArray = ['/' + config.latestVersion];
						for (const version of config.otherVersions) {
							pathNamespacesArray.push('/' + version);
						}
						return pathNamespacesArray;
					})(), // Run a self executed function to construct our pathNamespaces
				},
				namespaces: [
					{
						id: 'version',
						values: [config.latestVersion, ...config.otherVersions],
						optional: true,
						default: config.latestVersion,
						selector: '#version-selector',
					},
				],
				plugins: [
					function (hook, vm) {
						hook.init(() => {
							// REPLACE `LATEST` IN URL WITH LATEST VERSION
							window.addEventListener('hashchange', () => {
								const regexp = /#\/latest\/*(.*)/;
								location.href = location.href.replace(regexp, '#/' + config.latestVersion + '/$1');
							});

							// SET FAVICON
							document.querySelector('#favicon').href = config.favicon;

							// SET THEME COLOR FROM CONFIG
							const color = hexToHSL(config.themeColor);
							document.documentElement.style.setProperty('--theme-hue', color.hue);
							document.documentElement.style.setProperty('--theme-saturation', color.saturation);
							document.documentElement.style.setProperty('--theme-lightness', color.lightness);

							// COLOR THE SIDEBAR ARROWS PROPERLY
							const themeColor = window
								.getComputedStyle(document.documentElement)
								.getPropertyValue('--theme-color');

							document.documentElement.style.setProperty(
								'--sidebar-nav-pagelink-background-image--collapse',
								"url(\"data:image/svg+xml,%0A%3Csvg xmlns='http://www.w3.org/2000/svg' width='7' height='11.2' viewBox='0 0 7 11.2'%3E%3Cpath d='M1.5 1.5l4 4.1 -4 4.1' stroke-width='1.5' stroke='" +
									themeColor +
									"' fill='none' stroke-linecap='square' stroke-linejoin='miter' vector-effect='non-scaling-stroke'/%3E%3C/svg%3E\")"
							);

							document.documentElement.style.setProperty(
								'--sidebar-nav-pagelink-background-image--active',
								"url(\"data:image/svg+xml,%0A%3Csvg xmlns='http://www.w3.org/2000/svg' width='11.2' height='7' viewBox='0 0 11.2 7'%3E%3Cpath d='M1.5 1.5l4.1 4 4.1-4' stroke-width='1.5' stroke='" +
									themeColor +
									"' fill='none' stroke-linecap='square' stroke-linejoin='miter' vector-effect='non-scaling-stroke'/%3E%3C/svg%3E\")"
							);

							document.documentElement.style.setProperty(
								'--sidebar-nav-pagelink-background-image--loaded',
								"url(\"data:image/svg+xml,%0A%3Csvg xmlns='http://www.w3.org/2000/svg' width='11.2' height='7' viewBox='0 0 11.2 7'%3E%3Cpath d='M1.5 1.5l4.1 4 4.1-4' stroke-width='1.5' stroke='" +
									themeColor +
									"' fill='none' stroke-linecap='square' stroke-linejoin='miter' vector-effect='non-scaling-stroke'/%3E%3C/svg%3E\")"
							);
						});

						hook.doneEach(() => {
							// ENSURE CLICKING THE LOGO TAKES US TO THE CORRECT VERSION DOCS
							var appNameLink = Docsify.dom.find('#home-link');

							if (appNameLink) {
								appNameLink.href = vm.config.currentNamespace;
							}

							// ENSURE ANCHOR LINKS WILL STILL SCROLL INTO VIEW AS MANY TIMES AS NEEDED
							var sidebarLinks = document.getElementsByTagName('a');
							for (let link of sidebarLinks) {
								if (Docsify.util.isExternal(link.href) === false && link.id !== 'home-link') {
									link.addEventListener('click', (e) => {
										// Only trigger hash changes on parent "A" link elements
										if (e.target.tagName == 'A') {
											location.hash = '#dummy-hash'; // Dummy hash to change URL
											history.replaceState(null, '', e.currentTarget.href); // Replace dummy hash so the browser can jump to actual target
										} else {
											e.preventDefault();
											e.stopPropagation();
											e.currentTarget.click(); // Manually trigger a click event for the current target.
										}
									});
								}
							}

							// REMOVE BLANK ANCHOR LINKS SINCE THEY MESS UP SCROLLING
							const anchorLinks = document.querySelectorAll('.anchor');
							for (const anchorLink of anchorLinks) {
								if (anchorLink.clientWidth === 0 || anchorLink.clientHeight === 0) {
									anchorLink.remove();
								}
							}

							// ADD ANCHOR CLASS & DATA ID TO HEADER LINKS THAT DON'T HAVE THEM
							const headerLinks = document.querySelectorAll(
								'.content :is(h1, h2, h3, h4, h5, h6) a:not(.anchor)'
							);
							for (const headerLink of headerLinks) {
								headerLink.classList.add('anchor');
								headerLink.setAttribute('data-id', headerLink.parentElement.id);
							}
						});

						hook.mounted(() => {
							const versionSelector = Docsify.dom.find('#version-selector');

							// CONSTRUCT DATA FOR OUR VERSION SELECTOR
							const versionSelections = [];
							versionSelections.push({
								text: 'Latest (v' + config.latestVersion + ')',
								value: config.latestVersion,
								isDefault: true,
							});
							for (const version of config.otherVersions) {
								versionSelections.push({
									text: 'v' + version,
									value: version,
									isDefault: false,
								});
							}

							// POPULATE VERSION SELECTOR DROP DOWN
							versionSelections.map((version, index) => {
								versionSelector.options[index] = new Option(
									version.text,
									version.value,
									version.isDefault
								);
							});
						});
					},
				],
			};

			// HELPER FUNCTION FOR SETTING THEME COLOR
			hexToHSL = (H) => {
				// Convert hex to RGB first
				let r = 0,
					g = 0,
					b = 0;
				if (H.length == 4) {
					r = '0x' + H[1] + H[1];
					g = '0x' + H[2] + H[2];
					b = '0x' + H[3] + H[3];
				} else if (H.length == 7) {
					r = '0x' + H[1] + H[2];
					g = '0x' + H[3] + H[4];
					b = '0x' + H[5] + H[6];
				}
				// Then to HSL
				r /= 255;
				g /= 255;
				b /= 255;
				const cmin = Math.min(r, g, b);
				const cmax = Math.max(r, g, b);
				const delta = cmax - cmin;
				let h = 0;
				let s = 0;
				let l = 0;

				if (delta == 0) h = 0;
				else if (cmax == r) h = ((g - b) / delta) % 6;
				else if (cmax == g) h = (b - r) / delta + 2;
				else h = (r - g) / delta + 4;

				h = Math.round(h * 60);

				if (h < 0) h += 360;

				l = (cmax + cmin) / 2;
				s = delta == 0 ? 0 : delta / (1 - Math.abs(2 * l - 1));
				s = +(s * 100).toFixed(0);
				l = +(l * 100).toFixed(0);

				return {
					hue: h.toString(),
					saturation: s.toString() + '%',
					lightness: l.toString() + '%',
				};
			};
		</script>
		<!-- Docsify v4 -->
		<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
		<script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
		<script src="//unpkg.com/docsify-namespaced/dist/docsify-namespaced.min.js"></script>
		<script src="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/js/docsify-themeable.min.js"></script>
		<script src="//unpkg.com/docsify-footer-enh/dist/docsify-footer-enh.min.js"></script>
		<script src="https://cdn.jsdelivr.net/npm/docsify-tabs@1"></script>
		<script src="https://unpkg.com/prismjs@1.24.0/components/prism-gml.min.js"></script>
		<script src="https://unpkg.com/docsify-copy-code@2"></script>
		<style>
			/* https://jhildenbiddle.github.io/docsify-themeable/#/customization */
			:root {
				/* Basic */
				--base-background-color: #293338;
				--base-color: #f8f8f2;
				--base-font-weight: 400;
				--strong-color: #fff;
				--strong-font-weight: 600;
				--base-font-family: 'Roboto', sans-serif;
				--heading-font-family: 'Rubik', sans-serif;
				--heading-font-weight: 600;
				--heading-h1-font-weight: var(--heading-font-weight);
				--heading-h2-font-weight: var(--heading-font-weight);
				--heading-h3-font-weight: var(--heading-font-weight);
				--heading-h4-font-weight: var(--heading-font-weight);
				--heading-h5-font-weight: var(--heading-font-weight);
				--heading-h6-font-weight: var(--heading-font-weight);

				/* Content */
				--content-max-width: 80em;
				--link-color--hover: var(--theme-color);
				--heading-h1-color: var(--theme-color);
				--heading-h2-color: var(--theme-color);
				--heading-h2-border-color: var(--theme-color);
				--table-head-border-width: 1px;
				--table-cell-border-width: 1px;
				--table-row-odd-background: #242e33;

				/* Navbar */
				--navbar-root-border-color: var(--theme-color);
				--navbar-root-color: var(--theme-color);
				--navbar-root-color--hover: var(--background);
				--navbar-root-background--hover: var(--theme-color);
				--navbar-root-border-width: 1px;
				--navbar-root-border-radius: 5px;
				--navbar-root-padding: 5px;
				--navbar-root-margin: 0 0 0 1em;
			}
			#version-selector {
				background: var(--base-background-color); /* */
				color: var(--theme-color);
				border-color: var(--blockquote-border-color);
				border-radius: 5px;
				margin-top: 0.75em;
				padding: 0 5px;
				font-family: var(--code-font-family);
				font-size: 18px;
			}
			pre[class*='language-'] {
				border: 1px solid #1f282d;
				overflow: auto;
				position: relative;
			}
			a code:hover {
				color: var(--theme-color) !important;
				transition: 0.25s;
			}
			a code {
				transition: 0.25s;
			}
			a:hover {
				color: var(--theme-color);
				transition: 0.25s;
			}
			a {
				text-underline-offset: 2px;
				transition: 0.25s;
			}
			footer a {
				text-decoration: none !important;
				font-weight: bold;
			}
			
		/* !! -- alynne begin -- !! */
			
			.app-nav>ul>li>a:hover, .app-nav>ul>li>span:hover {
				color: #293338;
			}
			
			/* wrap function headers via gnysek */
			.markdown-section h2 a code:not([class*="lang-"]):not([class*="language-"]) {
				display: block;
				position: relative;
				white-space: normal;
				padding-left: 2ch;
				text-indent: -1ch;
				overflow: hidden;
			}
			
			/* line break indicators */
			.markdown-section h2 a code:after {    
				content: "\A ↳ \A ↳ \A ↳ \A ↳ \A ↳ \A ↳";
				position: absolute;
				left: 14px;
				top: 0;
				opacity: 0.5;
				white-space: break-spaces;
			}
			
			/* wrap function index */
			@media (max-width: 1440px) {
				[data-page*="/Function-Index."] .markdown-section li a { vertical-align:top; }
				
				[data-page*="/Function-Index."] .markdown-section code:not([class*=lang-]):not([class*=language-]) {
					white-space: break-spaces;
					display: inline-block;
					margin-bottom: -6px;
					padding: 0 6px 0 1.5ch;
					text-decoration: underline;
					text-indent: -1ch;
					position: relative;
					overflow: hidden;
				}

				[data-page*="/Function-Index."] .markdown-section li code:after {
					content: "\A ↳ \A ↳ \A ↳ \A ↳ \A ↳ \A ↳";
					position: absolute;
					left: 4px;
					top: 0;
					opacity: 0.5;
				}
			}
		</style>
		<script>
			/* Add word break opportunity after function opening parentheses */
			postLoad = function(){
				var titles = document.querySelectorAll('.markdown-section h2 a code:not([class*="lang-"]):not([class*="language-"]), .markdown-section li a code:not([class*="lang-"]):not([class*="language-"])');
				[].forEach.call(titles, function(title) {
					title.innerHTML = title.innerHTML.replace("(", "(<wbr>");
				});
			};
			
			window.addEventListener("load", function () {
			    window.setTimeout(postLoad, 1000);
			}, false);
		</script>
	</body>
</html>