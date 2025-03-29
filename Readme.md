# Расширения VS Code

Давайте разработаем свое первое расширение для среды VS Code!

Для начала инициализируем проект:
```bash
npx --package yo --package generator-code -- yo code
```

Далее выбираем:
```bash
# ? What type of extension do you want to create?
New Extension (TypeScript)
# ? What's the name of your extension?
Напишите любое название вашего расширения

# ? What's the identifier of your extension?
helloworld
# ? What's the description of your extension?
оставьте пустым
# ? Initialize a git repository?
Y
# ? Which bundler to use?
unbundled
# ? Which package manager to use?
npm
```


Открываем появившийся проект. Папку `src/tests` можем удалить. В файле `src/extension.ts` находится код нашего расширения. В функции `activate` находится скрипт расширения.

# Задача
Создать расширение, которое добавляет в Markdown:
- Кастомные стили CSS
- Поддержку пользовательского синтаксиса через markdown-it
- Интерактивные элементы через скрипты

### Материалы, которые понадобятся:
[Your first extension](https://code.visualstudio.com/api/get-started/your-first-extension)\
[Markdown Extension](https://code.visualstudio.com/api/extension-guides/markdown-extension)

### Задача 1: Стилизация превью (CSS)
Задача: Сделать превью похожим на стили GitHub + добавить анимации.

В package.json добавьте:
```json
"contributes": {
  "markdown.previewStyles": ["./styles/github-style.css"]
}
```
Создайте файл github-style.css:

Цвет фона: #f6f8fa

Заголовки h1: синий цвет + подчеркивание

Блоки кода: тень + плавная анимация при наведении

Кастомные стили для blockquote (например, левая бордюрная полоса)

Совет: Используйте [GitHub Primer CSS](https://primer.style/css/) как вдохновение.

### Задача 2: Добавление кастомного синтаксиса (markdown-it)
Задача: Реализовать поддержку:

:::alert Текст::: → <div class="alert">Текст</div>

???spoiler "Заголовок" → Скрываемый блок.

В package.json:

```json
"contributes": {
  "markdown.markdownItPlugins": true
}
```

В extension.ts:
```typescript
import * as vscode from 'vscode';
import markdownItContainer from 'markdown-it-container';

export function activate(context: vscode.ExtensionContext) {
  return {
    extendMarkdownIt(md: any) {
      // Alert blocks
      md.use(markdownItContainer, 'alert', {
        validate: () => true,
        render: (tokens: any, idx: number) => {
          return tokens[idx].nesting === 1 ? '<div class="alert">' : '</div>';
        }
      });

      // Spoiler blocks
      md.use(markdownItContainer, 'spoiler', {
        marker: '?',
        validate: () => true,
        render: (tokens: any, idx: number) => {
          // Логика для скрытия/раскрытия
          return tokens[idx].nesting === 1 ? 
            '<div class="spoiler"><details><summary>' : 
            '</summary></details></div>';
        }
      });

      return md;
    }
  };
}
```
Добавьте стили для .alert и .spoiler в CSS.

### Задача 3: Интерактивные элементы (JavaScript)
Задача: Сделать спойлеры интерактивными.

Требования:

В package.json:

```json
"contributes": {
  "markdown.previewScripts": ["./scripts/preview.js"]
}
```
Создайте preview.js:

```javascript
document.addEventListener('DOMContentLoaded', () => {
  document.querySelectorAll('.spoiler details').forEach(details => {
    details.addEventListener('toggle', () => {
      if (details.open) {
        details.style.animation = 'fadeIn 0.5s';
      }
    });
  });
});
```
Добавьте анимацию @keyframes fadeIn в CSS.

### Задача 4: Дополнительные задания
- Кастомные emoji: Добавьте :fire: → 🔥 через markdown-it-emoji.
- Диаграммы: Интегрируйте Mermaid.js через скрипты.
