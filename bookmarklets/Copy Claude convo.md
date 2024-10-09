To use, create a new bookmark in your browser's bookmark bar, edit the bookmark and paste all the code into the URL input:

![[bookmarklets.png]]

[More info](https://bookmarklets.org/what-is-a-bookmarklet/)


```javascript
javascript:(function() { 
  navigator.clipboard.writeText(formatText(getMessages()));

  function getMessages() {
    const userClass = 'font-user-message';
    const claudeClass = 'font-claude-message';
    return [...document.querySelectorAll('div')].filter(el =>
      el.classList.contains(userClass) || el.classList.contains(claudeClass)
    ).map(el => ({
      from: el.classList.contains(userClass) ? 'User' : 'Claude',
      isAI: el.classList.contains(claudeClass),
      message: parseElement(el)
    }));
  }

  function parseElement(el) {
    if (el.nodeType === Node.TEXT_NODE) {
      return el.textContent;
    }
    if (el.nodeName === 'EM') {
      return `*${el.textContent}*`;
    }
    if (el.nodeName === 'STRONG') {
      return `**${el.textContent}**`;
    }
    if (el.nodeName === 'LI') {
      return `- ${el.textContent.trim()}`;
    }
    if (el.nodeName === 'OL') {
      return [...el.children].map((li, i) => `${i + 1}. ${li.textContent.trim()}`).join('\n');
    }
    if (el.nodeName === 'UL') {
      return [...el.children].map(li => `- ${li.textContent.trim()}`).join('\n');
    }
    return [...el.childNodes].map(parseElement).join('');
  }

  function formatText(messages) {
    return messages.map(formatMessage).join('\n\n');
  }

  function formatMessage(m) {
    const formatted = `${m.message.trim()}`;
    return m.isAI ? formatted.replace(/(^|\n+)/g, (a, b, c) => a.length > 0 ? `\n> \n> ` : '> ') : formatted;
  }
})();
```