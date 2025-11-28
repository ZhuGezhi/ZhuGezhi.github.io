---
layout: page
title: Tags
description: 内容标签导航
header-img: "img/tag-bg.jpg"
permalink: /tags/
---

<!-- 页面头部背景 -->
<div class="tags-header" style="background-image: url('{{ site.baseurl }}/{% if page.header-img %}{{ page.header-img }}{% else %}{{ site.header-img }}{% endif %}')">
    <div class="tags-header-overlay">
        <div class="container">
            <h1 class="tags-title">标签云</h1>
            <p class="tags-subtitle">{{ page.description }}</p>
            
            <!-- 标签搜索框 -->
            <div class="tag-search-container">
                <input type="text" id="tagSearchInput" class="tag-search-input" 
                       placeholder="搜索标签..." oninput="filterTags()">
                <i class="fa fa-search tag-search-icon"></i>
            </div>
        </div>
    </div>
</div>

<!-- 主内容区 -->
<div class="tags-container container">
    <!-- 标签云展示 -->
    <div class="tag-cloud-section">
        <h2 class="section-title">所有标签</h2>
        <div id="tagCloud" class="tag-cloud">
            {% assign sorted_tags = site.tags | sort: 'size' | reverse %}
            {% for tag in sorted_tags %}
            <a href="#tag-{{ tag[0] | slugify }}" class="tag-cloud-item" 
               data-tag="{{ tag[0] | downcase }}">
                {{ tag[0] }} <span class="tag-count">({{ tag[1].size }})</span>
            </a>
            {% endfor %}
        </div>
    </div>

    <!-- 标签分类文章列表 -->
    <div class="tag-posts-section">
        <h2 class="section-title">按标签浏览</h2>
        <div id="tagPostsContainer" class="tag-posts-container">
            {% for tag in sorted_tags %}
            <div id="tag-{{ tag[0] | slugify }}" class="tag-post-group" data-tag="{{ tag[0] | downcase }}">
                <h3 class="tag-group-title">
                    <i class="fa fa-tag"></i> {{ tag[0] }} 
                    <span class="tag-group-count">({{ tag[1].size }}篇文章)</span>
                </h3>
                
                <div class="tag-post-list">
                    {% for post in tag[1] %}
                    <div class="tag-post-card">
                        <h4 class="post-card-title">
                            <a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
                        </h4>
                        <div class="post-card-meta">
                            <span class="post-date"><i class="fa fa-calendar"></i> {{ post.date | date: "%Y-%m-%d" }}</span>
                            {% if post.tags %}
                            <span class="post-tags"><i class="fa fa-tags"></i> 
                                {% for tag in post.tags limit: 2 %}
                                <span>{{ tag }}</span>{% unless forloop.last %}, {% endunless %}
                                {% endfor %}
                                {% if post.tags.size > 2 %}...{% endif %}
                            </span>
                            {% endif %}
                        </div>
                        {% if post.excerpt %}
                        <p class="post-card-excerpt">{{ post.excerpt | strip_html | truncate: 120 }}</p>
                        {% endif %}
                        <a href="{{ post.url | prepend: site.baseurl }}" class="post-card-readmore">阅读全文 →</a>
                    </div>
                    {% endfor %}
                </div>
            </div>
            {% endfor %}
            
            <!-- 空状态提示 -->
            {% if site.tags.size == 0 %}
            <div class="no-tags-message">
                <i class="fa fa-folder-open-o"></i>
                <h3>暂无标签</h3>
                <p>发布文章并添加标签后，这里会显示标签列表</p>
            </div>
            {% endif %}
        </div>
    </div>
</div>

<!-- JavaScript功能 -->
<script>
// 标签筛选功能
function filterTags() {
    const searchValue = document.getElementById('tagSearchInput').value.toLowerCase().trim();
    const tagItems = document.querySelectorAll('.tag-cloud-item');
    const tagGroups = document.querySelectorAll('.tag-post-group');
    
    // 记录是否有匹配的标签
    let hasMatch = false;
    
    // 筛选标签云和标签组
    tagItems.forEach(item => {
        const tagName = item.getAttribute('data-tag');
        if (tagName.includes(searchValue) || searchValue === '') {
            item.style.display = 'inline-block';
            hasMatch = true;
        } else {
            item.style.display = 'none';
        }
    });
    
    tagGroups.forEach(group => {
        const tagName = group.getAttribute('data-tag');
        if (tagName.includes(searchValue) || searchValue === '') {
            group.style.display = 'block';
        } else {
            group.style.display = 'none';
        }
    });
    
    // 显示无匹配提示
    const noResultMsg = document.getElementById('noResultMessage');
    if (!hasMatch && searchValue !== '') {
        if (!noResultMsg) {
            const container = document.querySelector('.tag-posts-container');
            const msg = document.createElement('div');
            msg.id = 'noResultMessage';
            msg.className = 'no-tags-message';
            msg.innerHTML = `
                <i class="fa fa-search"></i>
                <h3>未找到标签</h3>
                <p>没有找到包含 "${searchValue}" 的标签</p>
            `;
            container.appendChild(msg);
        }
    } else if (noResultMsg) {
        noResultMsg.remove();
    }
}

// 平滑滚动到标签位置
document.querySelectorAll('.tag-cloud-item').forEach(item => {
    item.addEventListener('click', function(e) {
        e.preventDefault();
        const targetId = this.getAttribute('href');
        document.querySelector(targetId).scrollIntoView({
            behavior: 'smooth',
            block: 'start'
        });
        
        // 高亮显示目标标签组
        const targetGroup = document.querySelector(targetId);
        targetGroup.classList.add('highlight');
        setTimeout(() => {
            targetGroup.classList.remove('highlight');
        }, 2000);
    });
});

// 页面加载完成后初始化
document.addEventListener('DOMContentLoaded', function() {
    // 初始化标签云大小
    const tagItems = document.querySelectorAll('.tag-cloud-item');
    tagItems.forEach(item => {
        const count = parseInt(item.querySelector('.tag-count').textContent.replace(/[()]/g, ''));
        const fontSize = Math.min(18 + count * 2, 28);
        item.style.fontSize = `${fontSize}px`;
    });
});
</script>

<!-- 样式 -->
<style>
/* 头部样式 */
.tags-header {
    background-size: cover;
    background-position: center;
    height: 300px;
    position: relative;
    margin-bottom: 40px;
}

.tags-header-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    text-align: center;
}

.tags-title {
    font-size: 48px;
    margin-bottom: 15px;
    text-shadow: 0 2px 4px rgba(0,0,0,0.3);
}

.tags-subtitle {
    font-size: 20px;
    margin-bottom: 30px;
    opacity: 0.9;
}

/* 搜索框样式 */
.tag-search-container {
    position: relative;
    max-width: 400px;
    margin: 0 auto;
}

.tag-search-input {
    width: 100%;
    padding: 12px 20px 12px 40px;
    border: none;
    border-radius: 30px;
    font-size: 16px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    outline: none;
}

.tag-search-icon {
    position: absolute;
    left: 15px;
    top: 50%;
    transform: translateY(-50%);
    color: #666;
}

/* 容器样式 */
.tags-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
}

.section-title {
    font-size: 24px;
    margin-bottom: 20px;
    padding-bottom: 10px;
    border-bottom: 2px solid #4299e1;
    color: #2d3748;
}

/* 标签云样式 */
.tag-cloud-section {
    margin-bottom: 60px;
}

.tag-cloud {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
    padding: 20px;
    background: #f8f9fa;
    border-radius: 8px;
}

.tag-cloud-item {
    display: inline-block;
    padding: 8px 15px;
    background: white;
    border-radius: 20px;
    color: #2d3748;
    text-decoration: none;
    box-shadow: 0 2px 5px rgba(0,0,0,0.05);
    transition: all 0.3s ease;
    position: relative;
}

.tag-cloud-item:hover {
    background: #4299e1;
    color: white;
    transform: translateY(-3px);
    box-shadow: 0 4px 12px rgba(66, 153, 225, 0.3);
}

.tag-cloud-item:hover .tag-count {
    color: white;
}

.tag-count {
    color: #718096;
    font-size: 0.8em;
    margin-left: 5px;
}

/* 标签文章列表样式 */
.tag-post-group {
    margin-bottom: 40px;
    padding: 20px;
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    transition: all 0.3s ease;
}

.tag-post-group.highlight {
    border: 2px solid #4299e1;
    box-shadow: 0 4px 15px rgba(66, 153, 225, 0.2);
}

.tag-group-title {
    font-size: 20px;
    margin-bottom: 20px;
    color: #2d3748;
}

.tag-group-count {
    font-size: 0.8em;
    color: #718096;
    font-weight: normal;
    margin-left: 10px;
}

/* 文章卡片样式 */
.tag-post-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
}

.tag-post-card {
    padding: 20px;
    border: 1px solid #e2e8f0;
    border-radius: 8px;
    transition: all 0.3s ease;
}

.tag-post-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 16px rgba(0,0,0,0.08);
    border-color: #4299e1;
}

.post-card-title {
    font-size: 18px;
    margin-bottom: 10px;
}

.post-card-title a {
    color: #2d3748;
    text-decoration: none;
    transition: color 0.3s;
}

.post-card-title a:hover {
    color: #4299e1;
}

.post-card-meta {
    font-size: 14px;
    color: #718096;
    margin-bottom: 10px;
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
}

.post-card-excerpt {
    color: #4a5568;
    margin-bottom: 15px;
    line-height: 1.6;
}

.post-card-readmore {
    display: inline-block;
    color: #4299e1;
    text-decoration: none;
    font-size: 14px;
    font-weight: 500;
    transition: all 0.3s;
}

.post-card-readmore:hover {
    color: #2b6cb0;
    text-decoration: underline;
}

/* 空状态样式 */
.no-tags-message {
    text-align: center;
    padding: 60px 20px;
    color: #718096;
}

.no-tags-message i {
    font-size: 60px;
    margin-bottom: 20px;
    color: #cbd5e0;
}

.no-tags-message h3 {
    font-size: 24px;
    margin-bottom: 10px;
}

/* 响应式调整 */
@media (max-width: 768px) {
    .tags-header {
        height: 200px;
    }
    
    .tags-title {
        font-size: 36px;
    }
    
    .tag-post-list {
        grid-template-columns: 1fr;
    }
    
    .tag-cloud {
        justify-content: center;
    }
}
</style>
