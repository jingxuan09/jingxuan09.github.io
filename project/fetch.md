# **Fetch API 完整教程**

Fetch API 是现代浏览器提供的用于发起网络请求的 JavaScript 接口，比传统的 `XMLHttpRequest` 更强大、更灵活。它基于 **Promise**，使得异步请求代码更简洁易读。

---

## **1. 基本 GET 请求**
```javascript
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => response.json()) // 解析 JSON 数据
  .then(data => console.log(data))  // 处理数据
  .catch(error => console.error('Error:', error)); // 错误处理
```
### **解析：**
- `fetch(url)` 发起 GET 请求，返回一个 `Promise`。
- `.then(response => response.json())` 解析响应为 JSON（也可以是 `text()`、`blob()` 等）。
- `.then(data => ...)` 处理最终数据。
- `.catch(error => ...)` 捕获错误。

---

## **2. POST 请求（提交数据）**
```javascript
fetch('https://jsonplaceholder.typicode.com/posts', {
  method: 'POST', // 指定方法
  headers: {
    'Content-Type': 'application/json', // 告诉服务器发送的是 JSON
  },
  body: JSON.stringify({ // 数据要转成 JSON 字符串
    title: 'foo',
    body: 'bar',
    userId: 1,
  }),
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```
### **关键参数：**
| 参数 | 说明 |
|------|------|
| `method` | `'GET'`, `'POST'`, `'PUT'`, `'DELETE'` 等 |
| `headers` | 设置请求头，如 `'Content-Type': 'application/json'` |
| `body` | 请求体数据（GET 请求不能有 `body`） |

---

## **3. 处理不同的响应类型**
Fetch 可以解析多种数据格式：
```javascript
// 解析 JSON
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data));

// 解析文本
fetch(url)
  .then(response => response.text())
  .then(text => console.log(text));

// 解析 Blob（如图片）
fetch('image.png')
  .then(response => response.blob())
  .then(blob => {
    const img = document.createElement('img');
    img.src = URL.createObjectURL(blob);
    document.body.appendChild(img);
  });
```

---

## **4. 错误处理**
Fetch **不会自动捕获 HTTP 错误（如 404、500）**，需要手动检查 `response.ok`：
```javascript
fetch('https://jsonplaceholder.typicode.com/404')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

---

## **5. 设置超时**
Fetch 默认没有超时机制，但可以用 `Promise.race` 实现：
```javascript
const fetchWithTimeout = (url, options, timeout = 5000) => {
  return Promise.race([
    fetch(url, options),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Request timeout')), timeout)
    ),
  ]);
};

fetchWithTimeout('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

---

## **6. 取消 Fetch 请求**
使用 `AbortController` 可以取消请求：
```javascript
const controller = new AbortController();
const signal = controller.signal;

fetch('https://jsonplaceholder.typicode.com/posts/1', { signal })
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted');
    } else {
      console.error('Error:', error);
    }
  });

// 2 秒后取消请求
setTimeout(() => controller.abort(), 2000);
```

---

## **7. 上传文件（FormData）**
```javascript
const formData = new FormData();
formData.append('file', fileInput.files[0]); // 文件
formData.append('username', 'John'); // 额外数据

fetch('https://example.com/upload', {
  method: 'POST',
  body: formData, // 不需要手动设置 Content-Type
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

---

## **8. 跨域请求（CORS）**
如果服务器支持 CORS，直接 `fetch` 即可：
```javascript
fetch('https://api.example.com/data') // 服务器需返回 Access-Control-Allow-Origin
  .then(response => response.json())
  .then(data => console.log(data));
```
如果服务器不支持 CORS，可以：
1. 使用 **JSONP**（仅 GET 请求）
2. 配置 **代理服务器**（如 Nginx）
3. 让后端开启 `Access-Control-Allow-Origin`

---

## **9. 与 Axios 对比**
| 特性 | Fetch | Axios |
|------|-------|-------|
| **浏览器支持** | 现代浏览器 | 所有浏览器（包括旧版） |
| **自动 JSON 解析** | 需要手动 `response.json()` | 自动解析 |
| **错误处理** | 不自动处理 HTTP 错误 | 自动处理 |
| **取消请求** | 用 `AbortController` | 用 `CancelToken` |
| **拦截器** | 无 | 支持请求/响应拦截 |

---

## **10. 总结**
- **Fetch 是现代浏览器原生支持的 API**，无需额外安装。
- **基于 Promise**，比 `XMLHttpRequest` 更简洁。
- **默认不处理 HTTP 错误**，需手动检查 `response.ok`。
- **支持取消请求（`AbortController`）和超时控制**。
- **适用于 REST API、文件上传等场景**。

### **推荐使用场景**
✅ 现代浏览器项目  
✅ 简单的 HTTP 请求  
✅ 不想引入额外库  

### **不推荐使用场景**
❌ 需要兼容 IE11（需用 `axios` 或 `XMLHttpRequest`）  
❌ 需要高级功能（如拦截器、自动错误处理）  

希望这个教程能帮助你掌握 Fetch API！ 🚀