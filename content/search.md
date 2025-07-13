+++
title = "搜索"
description = "搜索博客文章"
+++

# 搜索

<div class="search-container">
    <input type="text" id="search-input" placeholder="输入关键词搜索中文内容..." />
    <div id="search-results"></div>
</div>

<script src="https://cdn.jsdelivr.net/npm/fuse.js@6.6.2"></script>
<script>
// 中文搜索功能实现
(function() {
    const searchInput = document.getElementById('search-input');
    const searchResults = document.getElementById('search-results');
    let searchIndex = null;
    let searchData = null;

    // 加载搜索索引
    function loadSearchIndex() {
        fetch('/search_index.en.js')
            .then(response => response.text())
            .then(data => {
                // 执行JS代码获取搜索数据
                eval(data);
                if (window.searchIndex) {
                    searchData = window.searchIndex;
                    // 配置Fuse.js以支持中文搜索
                    const options = {
                        includeScore: true,
                        threshold: 0.3, // 降低阈值以提高中文匹配度
                        ignoreLocation: true,
                        keys: [
                            { name: 'title', weight: 0.7 },
                            { name: 'description', weight: 0.3 },
                            { name: 'content', weight: 0.1 }
                        ]
                    };
                    searchIndex = new Fuse(searchData, options);
                }
            })
            .catch(error => {
                console.error('搜索索引加载失败:', error);
            });
    }

    // 执行搜索
    function performSearch(query) {
        if (!searchIndex || !query.trim()) {
            searchResults.innerHTML = '';
            return;
        }

        const results = searchIndex.search(query);
        displayResults(results, query);
    }

    // 显示搜索结果
    function displayResults(results, query) {
        if (results.length === 0) {
            searchResults.innerHTML = '<p class="no-results">未找到相关结果</p>';
            return;
        }

        let html = `<h3>搜索结果 (${results.length})</h3>`;
        html += '<ul class="search-results-list">';

        results.forEach(result => {
            const item = result.item;
            const score = Math.round((1 - result.score) * 100);
            
            html += '<li class="search-result-item">';
            html += `<h4><a href="${item.permalink}">${highlightText(item.title, query)}</a></h4>`;
            html += `<div class="search-score">匹配度: ${score}%</div>`;
            
            if (item.description) {
                html += `<p class="search-description">${highlightText(item.description, query)}</p>`;
            }
            
            if (item.content) {
                const snippet = getSnippet(item.content, query);
                html += `<p class="search-content">${highlightText(snippet, query)}</p>`;
            }
            
            html += '</li>';
        });

        html += '</ul>';
        searchResults.innerHTML = html;
    }

    // 高亮搜索词
    function highlightText(text, query) {
        if (!query) return text;
        const regex = new RegExp(`(${escapeRegExp(query)})`, 'gi');
        return text.replace(regex, '<mark>$1</mark>');
    }

    // 获取内容片段
    function getSnippet(content, query) {
        const index = content.toLowerCase().indexOf(query.toLowerCase());
        if (index === -1) return content.substring(0, 150) + '...';
        
        const start = Math.max(0, index - 75);
        const end = Math.min(content.length, index + query.length + 75);
        return '...' + content.substring(start, end) + '...';
    }

    // 转义正则表达式特殊字符
    function escapeRegExp(string) {
        return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
    }

    // 事件监听
    searchInput.addEventListener('input', function() {
        const query = this.value;
        performSearch(query);
    });

    // 页面加载时初始化
    loadSearchIndex();
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
    padding: 15px;
    font-size: 16px;
    border: 2px solid #ddd;
    border-radius: 8px;
    margin-bottom: 20px;
    box-sizing: border-box;
}

#search-input:focus {
    outline: none;
    border-color: #007acc;
    box-shadow: 0 0 0 3px rgba(0, 122, 204, 0.1);
}

.search-results-list {
    list-style: none;
    padding: 0;
    margin: 0;
}

.search-result-item {
    margin-bottom: 25px;
    padding: 20px;
    border: 1px solid #eee;
    border-radius: 8px;
    background-color: #fafafa;
    transition: box-shadow 0.2s;
}

.search-result-item:hover {
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.search-result-item h4 {
    margin: 0 0 10px 0;
    font-size: 18px;
}

.search-result-item h4 a {
    color: #007acc;
    text-decoration: none;
}

.search-result-item h4 a:hover {
    text-decoration: underline;
}

.search-score {
    font-size: 12px;
    color: #666;
    margin-bottom: 8px;
}

.search-description {
    color: #666;
    margin: 8px 0;
    font-size: 14px;
}

.search-content {
    color: #888;
    font-size: 13px;
    margin: 8px 0;
    line-height: 1.4;
}

.no-results {
    text-align: center;
    color: #666;
    font-style: italic;
    padding: 40px;
}

mark {
    background-color: #ffeb3b;
    padding: 2px 4px;
    border-radius: 3px;
    font-weight: bold;
}

/* 响应式设计 */
@media (max-width: 600px) {
    .search-container {
        padding: 15px;
    }
    
    #search-input {
        font-size: 14px;
        padding: 12px;
    }
    
    .search-result-item {
        padding: 15px;
    }
}
</style> 