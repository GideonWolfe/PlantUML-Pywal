# PlantUML-Pywal
A locally hosted PlantUML server that uses system colors

![](https://i.imgur.com/fYrH15L.png)

# Purpose

I use [markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim), which is a great plugin for writing markdown in neovim and previewing it live in your browser.

In my [vim.reaper](https://github.com/GideonWolfe/vim.reaper) config, I have a custom stylesheet that allows the markdown preview to be styled based on your system colors. 

Now, I have extended this feature into PlantUML. For me, this solves two problems:

* Reduce reliance on public PlantUML servers
* Allow customization of generated images

# Usage

This PlantUML server runs in a docker container, and starts up in less than a second. 

Obviously you can go to the port itself (in my case `localhost:4928`) and plug in UML there. But these images won't look quite in context unless viewed through another application
using the system theme, such as `neovim` or a PDF viewer like `zathura`.

In order for the docker container to be aware of the new color file upon theme change, it must be restarted. I accomplish this with a hook in my [chameleon](https://github.com/GideonWolfe/Chameleon) config

```yaml
hooks:
  plantUML:
    command: "docker-compose -f docker-compose.yaml restart plantuml"
    directory: "/home/me/Projects/PlantUML-Pywal/"
```

EDIT: The container may not require a restart at all


# With markdown-preview

I have the following settings for my `markdown-preview.nvim` configuration:

```
let g:mkdp_preview_options = {
    \ 'mkit': {},
    \ 'katex': {},
    \ 'uml': {'server': 'http://localhost:4928'},
    \ 'maid': {},
    \ 'disable_sync_scroll': 0,
    \ 'sync_scroll_type': 'middle',
    \ 'hide_yaml_meta': 1,
    \ 'sequence_diagrams': {},
    \ 'flowchart_diagrams': {},
    \ 'content_editable': v:false,
    \ 'disable_filename': 0
    \ }

```

Note that the `uml` option has it's `server` variable pointed at our docker container. To switch back to the default public server, simply delete this line or dictionary item.

Once we are inside our markdown file, we have to "include" the `pywal` style. This can be done like so. 

```
@startuml
!include /include/plantuml.puml

< content here>

@enduml
```
Note that the config file created by `pywal` is mounted to `include/plantuml.puml`, so we are simply referencing that.

Now, no matter what system I am on, the diagrams I preview in my markdown will be appropriately colored.


