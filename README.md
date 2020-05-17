# Legxin

Legxin is an alternative UI for reddit. The goals are simple:

- Make programming fun again
I need to enjoy doing this. If I don't (hint: if I need to implement something I'm not interested in) I will quickly lose interest in it.

- Teach me [svelte](https://svelte.dev/) and web technologies
You can't be fullstack until you've succesfully centered a div. Also, svelte looks cool, so why not ?

- Be lightweight on the network
For some reason the standard UI (on old.reddit.com) retrieves ~1MB per page. Legxin uses the reddit API directly, so the data _should_ be smaller.

## Get started
*Note that you will need to have [Node.js](https://nodejs.org) installed.*

Install the dependencies...

```bash
cd legxin
npm install
```

...then start [Rollup](https://rollupjs.org):

```bash
npm run dev
```

Navigate to [localhost:5000](http://localhost:5000). You should see legxin running.

## What's in a name
Legxin is a ASCII-compliant spelling of leĝin, which is a contraction of "legis ĝin", which roughly translate to "[I] read it" in Esperanto. Naming is hard.
