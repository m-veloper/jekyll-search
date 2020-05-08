# jekyll-search

# 검색(Search) 기능 만들기

#### [ 본 포스팅은 기본적인 블로그가 만들어져 있다고 가정하고 진행합니다. ]

- 블로그에 글이 점점 많아지면서 내 자신도 내 글을 찾기가 점점 힘들어지고 있다는걸 깨달았습니다.

- 페이징을 하나하나 넘겨가면서 내 글을 찾아서 보는것이 상당히 비효율적입니다.

- 제 블로그에서 정보를 찾으시는 분들도 관련 글을 찾고 싶을 수도 있다는 생각에 검색 기능을 추가 하게 되었습니다.

- 참고 사이트
    - [한량넷]
    - [Hyeyeon]
    - [Simple-Jekyll-Search]

- [소스파일 보기]


### search.json 만들기

- root 경로(최상위 경로)에 search.json 를 만들고 소스 입력.

 - root 경로는 github.io로 끝나는 디렉토리를 말합니다.
 
 - jekyll 블로그게 만들어져 있다면  _config.yml 가 위치한 경로를 말합니다.

~~~javascript
---
layout: null
---
[
    {% for post in site.posts %}
     {
       "title"    : "{{ post.title | escape }}",
       "category" : "{{ post.category }}",
       "tags"     : "{{ post.tags | join: ', ' }}",
       "url"      : "{{ site.baseurl }}{{ post.url }}",
       "date"     : "{{ post.date }}"
     } {% unless forloop.last %},{% endunless %}
    {% endfor %}
]
~~~

<br>

### jekyll-search.js,  jekyll-search.min.js 만들기

- 파일은  [소스파일 보기] 에서 다운로드 받아주세요.
- assets/js/ 폴더에 들어있습니다.
- 각 블로그마다 js 파일을 어떤 경로에 위치 시켰는지 확인하시고 원하시는 경로에 파일들만 만들어주시면 될거같습니다.
- 참고로 assets/js/ 처럼 디렉토리를 따로 만들어서 위치 시켰습니다.

<br>

### _plugins 디렉토리 생성

- root 경로에 _plugins 디렉토리를 생성합니다.
- jekyll 블로그라면 _include, _layout 디렉토리가 있는 위치에 만들면 됩니다.

<br>

### simple_search_filter.rb 만들기

- 위에서 생성한 _plugins 디렉토리 안에다가 simple_search_filter.rb 을 만듭니다
- simple_search_filter.rb 파일에 소스 입력.

~~~ruby
module Jekyll
module CharFilter
 def remove_chars(input)
   input.gsub! '\\','&#92;'
   input.gsub! /\t/, '    '
   input.strip_control_and_extended_characters
 end
end
end

Liquid::Template.register_filter(Jekyll::CharFilter)

class String
def strip_control_and_extended_characters()
 chars.each_with_object("") do |char, str|
   str << char if char.ascii_only? and char.ord.between?(32,126)
 end
end
end
~~~

<br>


### root 경로에 search.html 만들기

- 제 블로그의 디렉토리 구조에 따라 만들어졌습니다. 원하시는 방법으로 다시 만드셔도 됩니다
- jekyll 블로그가 아직 잘 이해가 안되시면 그대로 따라하셔도 됩니다.
- 아래에 script를 불러오는 경로가 저와 다를 수 있습니다.
- 위에서 다운로드 받은 jekyll-search.js, jekyll-search.min.js가 들어있는 경로를 정확히 해주세요.

~~~javascript
---
layout: search
page_title: Search
permalink: /search/
---

<div class="container">
<div class="row">
 <div class="col-12">
   <div id="search-bar">
     <i class="fa fa-search" aria-hidden="true"></i>
     <input id="search-input" type="text" placeholder="Search..." />
   </div>
   <ul id="results-container"></ul>
 </div>
</div>
</div>

<!-- Script pointing to jekyll-search.js -->
<script src="/assets/js/jekyll-search.js" type="text/javascript"></script>

<script type="text/javascript">
SimpleJekyllSearch({
     searchInput: document.getElementById('search-input'),
     resultsContainer: document.getElementById('results-container'),
     json: '/search.json',
     searchResultTemplate: '<li><a href="{url}" title="{desc}" target="_blank">{title}</a></li>',
     noResultsText: 'No results found',
     limit: 10000,
     fuzzy: false,
     exclude: ['Welcome']
});
</script>
~~~

<br>

### search 레이아웃 만들기

- 위의 단계에서 root 경로에 만들었던 search.html 안에 layout: search 부분을 만드는 작업입니다.
- _layouts 디렉토리에 search.html 생성
- search.html에 코드 입력

~~~html
<!DOCTYPE html>
<html>
  <body>
      <div id="wrap">
            <!-- Main content -->
          <div class="main-layout">
              <div id="container-search">
                  <main>
                      {{ content }}
                  </main>
              </div>
          </div>
      </div>
  </body>
</html>
~~~

<br>

### 검색바 디자인하기

- "<i class="fa fa-search" aria-hidden="true"></i>" 를 위해서 [Font Awesome] 에서 해당파일들을 다운받아 주세요.
-  scss 

~~~scss
#search-bar {
  margin: 32px auto;
  border: 1px solid #ccc;
  border-radius: 20px;
  padding: 0 20px;

  & #search-input {
    width: calc(100% - 30px);
    border: none;
    line-height: 44px;
    outline: none;
  }
}
~~~

- css

~~~css
#search-bar {
    margin: 32px auto;
    border: 1px solid #ccc;
    border-radius: 20px;
    padding: 0 20px;
}

#search-bar #search-input {
    width: calc(100% - 30px);
    border: none;
    line-height: 44px;
    outline: none;
    border-style: none;
}
~~~

<br>

### 확인해보기

- 위의 경로대로 잘 만드셨다면 아래의 경로 입력해서 확인해보세요

~~~
http://127.0.0.1:4000/search/
~~~







[한량넷]: http://www.halryang.net/simple-jekyll-search/
[Hyeyeon]: https://imyeonn.github.io/blog/blog/30/
[Simple-Jekyll-Search]: https://github.com/christian-fei/Simple-Jekyll-Search
[소스파일 보기]: https://github.com/m-veloper/jekyll-search
[Font Awesome]: https://fontawesome.com/how-to-use/on-the-web/setup/hosting-font-awesome-yourself




