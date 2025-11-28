---
layout: page
title: Tags
description: 内容标签导航
header-img: "img/tag-bg.jpg"
permalink: /tags/
---

<div class="tags-container">
    <h1>所有标签</h1>
    
    <!-- 标签云 -->
    <div class="tag-cloud">
        {% assign sorted_tags = site.tags | sort %}
        {% for tag in sorted_tags %}
            <a href="#{{ tag[0] }}" class="tag-item">
                {{ tag[0] }} ({{ tag[1].size }})
            </a>
        {% endfor %}
    </div>
    
    <!-- 标签文章列表 -->
    <div class="tag-posts">
        {% for tag in sorted_tags %}
            <div id="{{ tag[0] }}" class="tag-section">
                <h2>{{ tag[0] }}</h2>
                <ul>
                    {% for post in tag[1] %}
                        <li>
                            <a href="{{ post.url }}">{{ post.title }}</a>
                            <span class="post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
                        </li>
                    {% endfor %}
                </ul>
            </div>
        {% endfor %}
    </div>
</div>

<style>
.tags-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

.tag-cloud {
    margin: 30px 0;
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.tag-item {
    padding: 5px 15px;
    background: #eee;
    border-radius: 20px;
    text-decoration: none;
    color: #333;
}

.tag-section {
    margin: 40px 0;
    padding-bottom: 20px;
    border-bottom: 1px solid #eee;
}

.tag-section ul {
    list-style: none;
    padding: 0;
}

.tag-section li {
    margin: 10px 0;
    padding: 10px;
    background: #f9f9f9;
    border-radius: 5px;
}

.post-date {
    color: #999;
    font-size: 0.8em;
    margin-left: 15px;
}
</style>
