## How to disable caching for frontend development in OctoPrint

By default, OctoPrint bundles JavaScript files to reduce the number of requests to the server, and will also cache the rendered
Jinja templates. However, if you're developing something and it's getting more and more annoying to keep restarting the server, 
you can disable this behaviour to make the development cycle faster.

### 1. Disable bundling in OctoPrint

Open `config.yaml` and find (or create) the `devel` section:

```yaml
devel:
    webassets:
        bundle: false
        minify: false
```

That will prevent OctoPrint from creating `packed_plugins.js` and others, and instead reference your individual file.

### 2. Disable caching in OctoPrint

To disable the caching of the Jinja templates, enter this configuration under devel with above:

```yaml
devel:
    cache:
        enabled: false
        preemptive: false
    webassets:
        bundle: false
        minify: false
```

> **➡️ This means the UI will load slower, so it's not recommended that you run this with lots of plugins or on a low powered 
> Raspberry Pi. It works for me on my Pi 4 and Windows 10 development machines quite well.**

### 3. Disabling caching in the browser

There's two things that you may need to do to get the browser to pull through modified files.

Simplest, you can use a hard refresh when the devtools is open by right clicking the reload:
![image](https://user-images.githubusercontent.com/31997505/111193638-47310580-85b2-11eb-9366-d32f1617b7ca.png)

You can also disable the cache in devtools, under the 'Network' tab:
![image](https://user-images.githubusercontent.com/31997505/111193768-70519600-85b2-11eb-887f-5801901ed12d.png)

I *think* this may only take effect when the devtools is open. However, in Microsoft Edge I have noticed requests going in for static assets
even when the devtools is closed. Didn't see the same in Chrome, I've not yet figured it out.
