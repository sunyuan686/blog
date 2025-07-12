+++
title = "搜索"
description = "搜索博客文章"
template = "page.html"
+++

# 搜索

<div class="search-container">
    <input type="text" id="search-input" placeholder="输入关键词搜索..." />
    <div id="search-results"></div>
</div>

<script type="text/javascript">
// 搜索功能实现
(function() {
    var searchInput = document.getElementById('search-input');
    var searchResults = document.getElementById('search-results');
    var searchIndex = null;
    var searchIndexData = null;

    // 加载搜索索引
    function loadSearchIndex() {
        var script = document.createElement('script');
        script.src = '/search_index.en.js';
        script.onload = function() {
            if (typeof elasticlunr !== 'undefined' && window.searchIndex) {
                searchIndex = elasticlunr(function() {
                    this.addField('title');
                    this.addField('description');
                    this.addField('content');
                    this.setRef('id');
                });
                
                searchIndexData = window.searchIndex;
                for (var i = 0; i < searchIndexData.length; i++) {
                    searchIndex.addDoc(searchIndexData[i]);
                }
            }
        };
        document.head.appendChild(script);
    }

    // 执行搜索
    function performSearch(query) {
        if (!searchIndex || !query.trim()) {
            searchResults.innerHTML = '';
            return;
        }

        var results = searchIndex.search(query, {
            fields: {
                title: {boost: 2},
                description: {boost: 1.5},
                content: {boost: 1}
            }
        });

        displayResults(results, query);
    }

    // 显示搜索结果
    function displayResults(results, query) {
        if (results.length === 0) {
            searchResults.innerHTML = '<p>未找到相关结果</p>';
            return;
        }

        var html = '<h3>搜索结果 (' + results.length + ')</h3>';
        html += '<ul class="search-results-list">';

        results.forEach(function(result) {
            var doc = searchIndexData[result.ref];
            html += '<li class="search-result-item">';
            html += '<h4><a href="' + doc.permalink + '">' + doc.title + '</a></h4>';
            if (doc.description) {
                html += '<p class="search-result-description">' + doc.description + '</p>';
            }
            if (doc.content) {
                var content = doc.content.substring(0, 150) + '...';
                html += '<p class="search-result-content">' + content + '</p>';
            }
            html += '</li>';
        });

        html += '</ul>';
        searchResults.innerHTML = html;
    }

    // 事件监听
    searchInput.addEventListener('input', function() {
        var query = this.value;
        performSearch(query);
    });

    // 加载elasticlunr库
    var elasticlunrScript = document.createElement('script');
    elasticlunrScript.src = '/elasticlunr.min.js';
    elasticlunrScript.onload = loadSearchIndex;
    document.head.appendChild(elasticlunrScript);
})();
</script>

<style>
.search-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

#search-input {
    width: 100%;
    padding: 12px;
    font-size: 16px;
    border: 2px solid #ddd;
    border-radius: 6px;
    margin-bottom: 20px;
}

#search-input:focus {
    outline: none;
    border-color: #007acc;
}

.search-results-list {
    list-style: none;
    padding: 0;
}

.search-result-item {
    margin-bottom: 20px;
    padding: 15px;
    border: 1px solid #eee;
    border-radius: 6px;
    background-color: #fafafa;
}

.search-result-item h4 {
    margin: 0 0 10px 0;
}

.search-result-item h4 a {
    color: #007acc;
    text-decoration: none;
}

.search-result-item h4 a:hover {
    text-decoration: underline;
}

.search-result-description {
    color: #666;
    margin: 5px 0;
}

.search-result-content {
    color: #888;
    font-size: 14px;
    margin: 5px 0;
}
</style> 