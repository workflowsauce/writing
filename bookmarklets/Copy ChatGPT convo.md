To use, create a new bookmark in your browser's bookmark bar, edit the bookmark and paste all the code into the URL input:

![[../assets/bookmarklets.png]]

[More info](https://bookmarklets.org/what-is-a-bookmarklet/)

```javascript
javascript:(function() { 
  navigator.clipboard.writeText(formatText(getMessages()));

  function getMessages() {
    const userClass = '[data-message-author-role="user"]';
    const assistantClass = '[data-message-author-role="assistant"]';
    return [...document.querySelectorAll('div[data-message-author-role]')].map(el => ({
      from: el.matches(userClass) ? 'User' : 'Assistant',
      isAI: el.matches(assistantClass),
      message: parseElement(el)
    }));
  }

  function parseElement(el) {
    if (el.nodeType === Node.TEXT_NODE) {
      return el.textContent;
    }
    if (el.nodeName === 'EM') {
      return `*${el.textContent}*`;  // italics
    }
    if (el.nodeName === 'STRONG') {
      return `**${el.textContent}**`;  // bold
    }
    if (el.nodeName === 'LI') {
      return `\n- ${el.textContent.trim()}`;  // bullet point
    }
    if (el.nodeName === 'OL') {
      return `\n` + [...el.children].map((li, i) => `${i + 1}. ${li.textContent.trim()}`).join('\n');
    }
    if (el.nodeName === 'UL') {
      return `\n` + [...el.children].map(li => `- ${li.textContent.trim()}`).join('\n');
    }
    // ensure paragraphs are separated by double line breaks
    if (el.nodeName === 'P' || el.nodeName === 'DIV') {
      return `\n${[...el.childNodes].map(parseElement).join('')}\n`;
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