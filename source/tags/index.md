---
title: tags
date: 2016-06-20 23:25:53
---
<div class="article-entry" itemprop="articleBody">
    <% if (post.excerpt && index){ %>
        <%- post.excerpt %>
    <% } else { %>
        <% if (page.path === "tags/index.html"){ %>
            <引入 tags 页面的代码>
        <% } %>
        <%- post.content %>
    <% } %>
</div>
<%- tagcloud({
    min_font: 16,
    max_font: 35,
    amount: 999,
    color: true,
    start_color: 'gray',
    end_color: 'black',
}) %>
