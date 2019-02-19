# HubL Visual Studio Code Language Extension
Get it :point_right: https://marketplace.visualstudio.com/items?itemName=WilliamSpiro.hubl  

[HubL](https://designers.hubspot.com/docs/hubl/intro-to-hubl) is the underlying syntax used in the [HubSpot content management system](https://www.hubspot.com/products/marketing/content-management-system). HubL can be used to dynamically render content, as well as generate easy to edit modules within the content editors marketers work in so they do not ever need to touch the underlying HTML of a template. This allows for many pages to use a single template, making managing content and websites significantly easier and quicker.

This is a HubL language extension for the [Visual Studio Code IDE](https://code.visualstudio.com/), allowing for :rocket: fast local HubSpot CMS Platform development in `.html` files. For comprehensive HubL documentation, see the [HubL docs](https://designers.hubspot.com/docs/hubl/intro-to-hubl).

All HubL snippets are auto-generated by the `hubl_snippets_gen.py` script from `@JinjavaDoc` annotations to ensure the snippets are always up to date, and easy to maintain :potable_water:  

![language extention demo](https://cdn2.hubspot.net/hubfs/2359872/IMPORTANT/DONOTDELETE/hubl-language-extension/nifty_gif.gif)  
_For nice HubL syntax highlighting, install the [Jinja scopes extension](https://marketplace.visualstudio.com/items?itemName=wholroyd.jinja) which lays on top of `.html` files._

## Features
### __HubL Snippets__  
All HubL supported tags, filters, expression tests and functions have auto-complete snippts. Expression tests are accessed by typing the test name alone, filters are accessed with `|` and fucntions/tags are acccessed with `~`. All snippets include descriptions and parameter details. You up/down arrow to navigate the IntelliSense and hit enter to execute a snippet. Snippet completed HubL statements will auto-highlight avaiable parameters, which can be tabbed through (`${parameter}`).     

_NOTE_: HubL tags, functions, expression tests and filters are all pulled from the `cos-rendering/v1/hubldoc` api, so do not update any `snippets/auto-gen/...` json files manually. Run `hubl_snippets_gen.py` to re-generate these JSON files when HubL changes occur. `snippets/man_gen/...` files are for any extra/helpful snippets used in HubL - these files are maintained manually.

![Parameters](https://cdn2.hubspot.net/hubfs/2359872/IMPORTANT/DONOTDELETE/hubl-language-extension/params.png)
[__HubL Tags__](https://designers.hubspot.com/docs/hubl/hubl-supported-tags) produce entire HubL tag statements with available parameters. Ex `~he` > Enter produces:
```
{% header "${my_header}" 
   header_tag="${header_tag}",
   value="${value}" 
%}
```
[__HubL Filters__](https://designers.hubspot.com/docs/hubl/hubl-supported-filters) produce entire HubL filter statements with available parameters. Ex `|se` > Enter produces:
```
|selectattr("${attr}", ${exp_test})
```
[__HubL Functions__](https://designers.hubspot.com/en/docs/hubl/hubl-supported-functions) produce entire HubL function statements with available parameters, without wrapping curly braces. The intenion of this is so you can  use HubL functions within other HubL statements easily (like setting variables, for loops, etc.) Ex `~hub` > Enter produces:
```
hubdb_table_rows(${table_id}, ${query})
```
[__HubL Expression Tests__](https://designers.hubspot.com/docs/hubl/operators-and-expression-tests#expression-tests) produce expression test names. Ex `di` > Enter produces:
```
divisibleby
```

__Other Helpful HubL Things__  
[`standard_footer_includes` & `standard_header_includes`](https://designers.hubspot.com/docs/hubl/hubl-supported-variables#required-page-template-variables)
 ```
{{ standard_footer_includes }}
{{ standard_header_includes }}
 ```
[`for`](https://designers.hubspot.com/docs/hubl/for-loops)
```
{% for ${iterable} in ${dict} %}
   {{ ${iterable} }}
{% endfor %}
```
[`if`](https://designers.hubspot.com/docs/hubl/if-statements)
```
{% if ${test} %}
    ${do_something}
{% endif %}
```
[`elif` & `else`](https://designers.hubspot.com/docs/hubl/if-statements#using-elif-and-else)
```
{% elif ${test} %}
{% else %}
```
[`blog variables`](https://designers.hubspot.com/docs/hubl/hubl-supported-variables#blog-variables)
```
{{ content.post_body }}
{{ content.blog_post_author }}
{{ group }}
{{ next_page_num }}
etc.
```
_NOTE_: Some of these variables are nested  
![Nested variables](https://cdn2.hubspot.net/hubfs/2359872/IMPORTANT/DONOTDELETE/hubl-language-extension/content..gif)

`hubldoc`
```
<!doctype html>
<!--[if lt IE 7]> <html class="no-js lt-ie9 lt-ie8 lt-ie7" lang="{{ html_lang }}" {{ html_lang_dir }}> <![endif]-->
<!--[if IE 7]>    <html class="no-js lt-ie9 lt-ie8" lang="{{ html_lang }}" {{ html_lang_dir }}>        <![endif]-->
<!--[if IE 8]>    <html class="no-js lt-ie9" lang="{{ html_lang }}" {{ html_lang_dir }}>               <![endif]-->
<!--[if gt IE 8]><!--> <html class="no-js" lang="{{ html_lang }}" {{ html_lang_dir }}> <!--<![endif]-->
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
        <meta name="author" content="{{ meta_author }}">
        <meta name="description" content="{{ page_meta.meta_description }}">
        <title>{{ page_meta.html_title }}</title>
        {% if site_settings.favicon_src %}<link rel="shortcut icon" href="{{ site_settings.favicon_src }}" />{% endif %}
        {{ standard_header_includes }}
    </head>
    <body>
        ${stuff}
        {{ standard_footer_includes }}
    </body>
</html>
```
`hublblog`
```
{% if is_listing_view %}
    <!-- Markup for blog listing template -->
    {% for content in contents %}
        <h2><a href="{{content.absolute_url}}">{{ content.name }}</a></h2>
        {% if content.post_list_summary_featured_image %}
            <a href="{{content.absolute_url}}">
                <img src="{{ content.post_list_summary_featured_image }}" alt="{{ content.featured_image_alt_text }}">
            </a>
        {% endif %}
        {{ content.post_list_content|safe }}
    {% endfor %}
{% else %}
    <!-- Markup for blog post template -->
    <h1>{{ content.name }}</h1>
    <a href="{{ group.absolute_url }}/author/{{ content.blog_post_author.slug }}">{{ content.blog_post_author.display_name }}</a>
    {{ content.publish_date_localized }}
    {{ content.post_body }}
    {% for topic in content.topic_list %}
        <a href="{{ blog_tag_url(group.id, topic.slug) }}">{{ topic.name }}</a>{% if not loop.last %},{% endif %}
    {% endfor %}
    {% blog_comments "blog_comments" overrideable=False, label='Blog Comments' %}
{% endif %}
```
[`request.<variables>`](https://designers.hubspot.com/docs/hubl/hubl-supported-variables#http-request-variables)
```
{{ request.cookies }}
{{ request.domain }}
{{ request.full_url }}
etc.
```
[`Email Required Template Variables`](https://designers.hubspot.com/docs/hubl/hubl-supported-variables#required-email-template-variables)
```
{{ site_settings.company_city }}
{{ site_settings.company_name }}
{{ unsubscribe_link }}
{{ unsubscribe_link_all }}
etc.
```

__Custom Module Fields__
In effort to allow for more streamlined local development of custom modules, snippets have been added that can be added to the fields.json file within a custom module.
The snippet trigger roughly corresponds with the field names in the design manager.  
For example:  to add a rich text field, you would type 
```
field.richtext
```
To create a group, use 
```
group.group
```
To create a repeater, use
```
group.repeater
```
all associated fields need to be added into the children array within the snippet output



_NOTE_: If you are having trouble getting IntelliSense suggestions when in snippet placeholders you may need to add the following to your [User Settings](https://code.visualstudio.com/docs/getstarted/settings) `"editor.suggest.snippetsPreventQuickSuggestions": false`. If parameter suggestions are not showing up, set `"editor.parameterHints": true`.

### __HubL Langage Configuration__  
`langconfig/language-configuration.json` contains some nice to haves when it comes to writing HubL. This supports auto completes of all HubL statement types and supports HubL statement swrapping (Supports `{%%}`,`{##}`,`{{}}`). Additionally, makes commenting (CMD + /) create HubL comments. Install the [Jinja scopes extension](https://marketplace.visualstudio.com/items?itemName=wholroyd.jinja) for pretty HubL syntax highlighting in .html files. 

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Contributing
Anyone should feel free to fork/PR this! Open source for the win :poop::poop::poop::poop:.
Please make sure and explain your changes thoroughly, update version and changelog where needed. See above note about `hubl_snippets_gen.py` for updating HubL `snippets/auto-gen/...` json files.   

To run locally, `git clone` repo, open in Visual Studio Code and press `f5` to launch a new VSCode window with the extension installed. `CMD` + `R` to reload the window after having made any changes.  