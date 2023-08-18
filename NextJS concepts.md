## Rendering
By default NextJS pre-renders every page. This means that HTML is generated for each page in advance.
NextJS has two forms of pre-rendering:
- **Static Generation**: the HTML is generated at build time and will be reused on each request
- **Server-side Rendering**: the HTML is generated on each request

Is recommended to use Static Generation over Server-side Rendering for performance reasons. Statically generated pages can be cached by [CDN (Content Delivery Network)](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/) wit no extra configuration to boost performance.
### Link
Link is a React component that extends the HTML `<a>` element to provide prefetching and client-side navigation between routes. It is the primary way to navigate between routes in NextJS.

A very interesting property about `Link` components is `prefetch` which by default is set to be true. This property allows the app to prefetch the page in the background.

### getStaticProps
If you export a function called `getStaticProps`, for Static Site Generation, from a page NextJS will pre-render this page at build time using the props return by `getStaticProps`.
An example of this is:
```js
export function async getStaticProps() {
	const res = await fetch('https://api.github.com/repos/vercerl/next.js')
	const repo = await res.json()
	return { props: { repo } }
}

export default function Page({ repo }) {
	return repo.stargazers_count;
}

```

When to use `getStaticProps`:
- The data required to render the page is available ahead of user's request
- The data comes from a headless [CMS (Content Management Service)](https://kinsta.com/knowledgebase/content-management-system/)
- The data must be pre-rendered and be very fast (html and json generation)
- The data can be publicly cached (can be bypassed with Middleware)

A very interesting property of `getStaticProps` is the property `revalidate` which allows us to re-render the data and call this function again when the time entered as value has passed and we refresh the page ([ISR which stands for Incremental Static Regeneration](https://nextjs.org/docs/pages/building-your-application/rendering/incremental-static-regeneration)). An example of this is:
```js
export function async getStaticProps() {
	const res = await fetch('https://api.github.com/repos/vercerl/next.js')
	const repo = await res.json()
	return { 
		props: { 
			repo 
		},
		revalidate: 10
	}
}

export default function Page({ repo }) {
	return repo.stargazers_count;
}

```
The code above will call the `getStaticProps` function every 10 second ahead of the last call on this function.



<details>
	<summary><h1 style="display: inline-block">Want to ruin the surprise?</h1></summary>
	
```javascript 
export async function getServerSideProps(context) {
	const res = await fetch('https://api.github.com/repos/vercerl/next.js')
	const data = await res.json()

	if (!data) {
		return {
			notFound: true
		}
	}

	return { 
		props: { 
			repo 
		}
	}
}
```

</details