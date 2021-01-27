---
id: fetch
title: Fetch
sidebar_label: Fetch
---

The Fetch API provides an interface for fetching resources (including across the network)  
[See MDN Docs &#8594](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

Next.js has [built-in fetch support](https://nextjs.org/blog/next-9-4#improved-built-in-fetch-support) both in browser and node environment.

It can be used both in your components and Next.js server-side methods.

```js title="In your component"
export const FetchExample = () => {
    const [error, setError] = useState(null);
    const [isLoaded, setIsLoaded] = useState(false);
    const [data, setData] = useState([]);

    useEffect(() => {
        fetch(API_URL)
            .then((res) => {
                console.log("res: ", res);
                return res.json();
            })
            .then(
                (result) => {
                    console.log("result: ", result);
                    setData(result);
                    setIsLoaded(true);
                },
                // Note: it's important to handle errors here
                // instead of a catch() block so that we don't swallow
                // exceptions from actual bugs in components.
                (error) => {
                    setIsLoaded(true);
                    setError(error);
                },
            );
    }, []);
}
```

```js title="Next.js getStaticProps"
export async function getStaticProps() {
  // fetch no longer needs to be imported from isomorphic-unfetch
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts
    }
  }
}

function Blog({ posts }) {
  // Render posts...
}

export default Blog
```
