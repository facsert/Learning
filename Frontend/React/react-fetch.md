---
pubDatetime: 2024-04-29 22:39:27
title: React fetch
slug: React fetch
featured: false
draft: false
tags:
  - React
description: "React fetch"
---

## 介绍

## 自定义封装

```ts
type Server = {
    base: string
    backup: string
    cdn: string
}

const servers: Server = {
    base: 'http://192.168.1.100:8080',
    backup: 'http://192.168.1.100:8090',
    cdn: 'http://192.168.1.100:8060',
}

class Response<T> {
    constructor(
        public result: boolean = false,
        public msg: string = '',
        public content: T ,
    ) {};
}

async function FetchGet<T>(api: string, server: keyof Server='base'): Promise<Response<T>> {
    try {
        const response = await fetch(servers[server] + api)
        if (!response.ok) {
            throw new Error(`GET ${api} ${response.status}`)
        }
        return await response.json() as Response<T>
    } catch(err) {
        return new Response<T>(false, String(err), null as T)
    }
};

async function FetchPost<T>(api: string, body: unknown, server: keyof Server='base'): Promise<Response<T>> {
    try {
        const response = await fetch(servers[server] + api, {
            method: 'POST',
            headers: {'Content-Type': 'application/json', 'Cache-Control': 'no-cache'},
            body: JSON.stringify(body),
        })
        if (!response.ok) {
            throw new Error(`GET ${api} ${response.status}`)
        }
        return await response.json() as Response<T>
    } catch(err) {
        return new Response(false, String(err), null as T)
    }
};

async function FetchFile<T>(api: string, server: keyof Server='base'): Promise<Response<T>> {
    try {
    const response = await fetch(servers[server] + api);
    if (!response.ok) {
        throw new Error(`GET ${api} ${response.status}`)
    } 

    const disposition = response.headers.get('Content-Disposition');
    let fileName = 'downloaded_file';
    if (disposition && disposition.includes('filename=')) {
        fileName = disposition.split('filename=')[1]
    }

    const blob = await response.blob()
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = fileName;
    document.body.appendChild(a);
    a.click();
    a.remove();
    window.URL.revokeObjectURL(url);

    return new Response(true, `download ${fileName} success`, null as T)
    } catch (error) {
    return new Response(false, String(error), null as T)
    };
};

export { servers, FetchGet, FetchPost, FetchFile, Response}
```
