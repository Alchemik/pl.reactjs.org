---
id: hooks-overview
title: Hooki w pigułce
permalink: docs/hooks-overview.html
next: hooks-state.html
prev: hooks-intro.html
---

*Hooki* są nowym dodatkiem w Reakcie 16.8. Pozwalają one używać stanu i innych funkcjonalności Reacta, bez użycia klas.

Hooki są [kompatybilne wstecznie](/docs/hooks-intro.html#no-breaking-changes). Ten rozdział zawiera przegląd wiedzy o Hookach i przeznaczony jest dla doświadczonych użytkowników Reacta. Szybki przegląd. Jeżeli poczujesz się zagubiony, szukaj żółtych ramek, takich jak ta poniżej:


>Szczegółowe objaśnienia
>
>Przeczytaj podrozdział ["Motywacja"](/docs/hooks-intro.html#motivation), aby dowiedzieć się dlaczego dodaliśmy Hooki

**↑↑↑ Każdy podrozdział zakończony jest taką żółtą ramką.** Zawierają one linki do szczegółowych objaśnień.

## 📌 Hook Stanu {#state-hook}

Ten przykład renderuje licznik. Kiedy wciskasz przycisk, zwiększa on wartość w stanie.

```js{1,4,5}
import React, { useState } from 'react';

function Example() {
  // Zadeklaruj nową zmienną stanu, którą nazwiemy "count"
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Kliknąłeś {count} razy</p>
      <button onClick={() => setCount(count + 1)}>
        Naciśnij mnie
      </button>
    </div>
  );
}
```

W tym przykładzie `useState` jest *Hookiem* (za chwilę wyjaśnimy, co to znaczy). Wywołujemy go z wewnątrz komponentu funkcyjnego, aby wzbogacić go o lokalny stan. React zachowa ten stan pomiędzy kolejnymi renderowaniami. `useState` zwraca parę: *aktualną* wartość stanu i funkcję, która pozwala ci go aktualizować. Następnie możesz wywołać te funkcję w procedurze obsługi zdarzenia albo z innego miejsca. Działa to mniej więcej tak samo, jak `this.setState` w komponencie klasowym, z tą różnicą, że nie scala on starych i nowych wartości. (Omówimy te różnice w rozdziale ["Używanie Hooka Stanu"](/docs/hooks-state.html).)

Jedynym argumentem funkcji `useState` jest stan początkowy. W przykładzie powyżej jest to `0`, ponieważ nasz licznik startuje od zera. Zauważ, że w przeciwieństwie to `this.state` stan nie musi być obiektem -- ale nic nie stoi na przeszkodzie, by był. Wartość argumentu ze stanem początkowym jest wykorzystywana tylko przy pierwszym renderze.

#### Deklaracja kilku zmiennych stanu {#declaring-multiple-state-variables}

Możesz użyć Hooka Stanu kilka razy w tym samym komponencie:

```js
function ExampleWithManyStates() {
  // Zadeklaruj kilka zmiennych stanu!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

Dzięki składni [przypisania destrukturyzującego tablic](https://developer.mozilla.org/pl/docs/Web/JavaScript/Referencje/Operatory/Destructuring_assignment#Destrukturyzacja_tablic) możemy nadać różne nazwy zmiennym stanu, które zadeklarowaliśmy wywołując funkcję `useState`. Nazwy te nie są częścią interfejsu API `useState`. Zamiast tego React zakłada, że jeżeli wywołujesz funckję `useState` wielokrotnie, za każdym razem (przy każdym renderze) robisz to w tej samej kolejności. W dalszej części tego rozdziału omówimy jak to działa i dlaczego jest to przydatne.

#### Ale czym jest Hook? {#but-what-is-a-hook}

Hooki są to funkcje, które pozwalają "zahaczyć się" w mechanizmy stanu i cyklu życia Reacta, z wewnątrz komponentów funkcyjnych. Hooki nie działają w klasach -- pozwalają ci korzystać z Reacta bez klas. ([Nie zalecamy](/docs/hooks-intro.html#gradual-adoption-strategy) przepisywania istniejących komponentów z dnia na dzień, ale jeżeli masz ochotę, możesz zacząć korzystać z Hooków w nowych komponentach.)

React dostarcza kilka wbudowyanch Hooków, jak `useState`. Ale możesz też stworzyć własne Hooki, by współdzielić zachowanie związane ze stanem pomiędzy komponentami. Najpierw rzućmy jednak okiem na wbudowane Hooki.

>Szczegółowe objaśnienia
>
>Hookowi Stanu poświęciliśmy cały rozdział: ["Używanie Hooka Stanu"](/docs/hooks-state.html).

## ⚡️ Hook Efektu {#effect-hook}

Najprawdopodobniej pobierałeś już dane, obsługiwałeś subskrypcje lub ręcznie modyfikowałeś drzewo DOM z wewnątrz komponentów reactowych. Tego typu operacje nazywamy ["efektami ubocznymi"](https://www.wikiwand.com/pl/Skutek_uboczny_(informatyka)) (ang. *side effects*), ponieważ mogą one wpływać na inne komponenty i nie mogą zostać przeprowadzone podczas renderowania.

Hook Efektu `useEffect` daje możliwość przeprowadzania "efektów ubocznych" z wewnątrz komponentu funkcyjnego. Pełni on taką samą funkcję, jak `componentDidMount`, `componentDidUpdate` i `componentWillUnmount` w komponentach klasowych, ale uproszczoną do jednego interfejsu API. (Różnice między nimi pokażemy na przykładach w rozdziale ["Używanie Hooka Efektu"](/docs/hooks-effect.html).)

Na przykład, ten komponent ustawia tytuł dokumentu HTML po tym, jak React zaktualizuje drzewo DOM:

```js{1,6-10}
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Podobnie jak componentDidMount i componentDidUpdate:
  useEffect(() => {
    // Zaktualizuj tytuł dokumentu, korzystając z interfejsu API przeglądarki
    document.title = `Kliknąłeś ${count} razy`;
  });

  return (
    <div>
      <p>Kliknąłeś {count} razy</p>
      <button onClick={() => setCount(count + 1)}>
        Naciśnij mnie
      </button>
    </div>
  );
}
```

Kiedy wywołujesz funkcję `useEffect`, mówisz Reactowi, żeby uruchomił twój "efekt" @todo zmian w drzewie DOM. Efekty są zadeklarowane wewnątrz komponentu, więc mają dostęp do jego właściwości (ang. *props*) i stanu. Domyślnie React uruchomi wszystkie efekty po każdym renderowaniu -- *włącznie* z pierwszym. (Różnice między tym, a metodami cyklu życia komponentów klasowych omówimy w rozdziale [["Używanie Hooka Efektu"](/docs/hooks-effect.html).)

Efekty mogą też określić w jaki sposób "sprzątać" po nich, poprzez zwrócenie funkcji. Na przykład, ten komponent używa efektu, aby zasubskrybować się do informacji o dostępności znajomego. A następnie sprząta po sobie, anulując subskrypcję.

```js{10-16}
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);

    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Ładowanie...';
  }
  return isOnline ? 'Dostępny' : 'Niedostępny';
}
```

W tym przykładzie React anuluje subskrypcję naszego `ChatAPI`, kiedy komponent zostaje odmontowany. A także przed każdym kolejnym uruchomieniem efektu (które następuje przy każdym przerenderowaniu komponentu). (Jeśli chcesz, możesz [powiedzieć Reactowi żeby tego nie robił](/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects), jeśli wartość `props.friend.id`, którą przekazaliśmy do `ChatAPI` się nie zmieniła.)

Podobnie jak z `useState`, możesz użyć więcej niż jednego efektu w swoim komponencie:

```js{3,8}
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `Kliknąłeś ${count} razy`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
```

Hooki pozwalają ci organizować kod wewnątrz komponentu, bazując na powiązanych ze sobą fragmentach (takich jak dodawanie i anulowanie subskrypcji). Nie wymuszają sztucznego podziału, jak metody cyklu życia (ang. *lifecycle methods*).

>Szczegółowe objaśnienia
>
>Możesz dowiedzieć się więcej o `useEffect` w rozdiale: ["Używanie Hooka Efektu"](/docs/hooks-effect.html).

## ✌️ Rules of Hooks {#rules-of-hooks}

Hooks are JavaScript functions, but they impose two additional rules:

* Only call Hooks **at the top level**. Don’t call Hooks inside loops, conditions, or nested functions.
* Only call Hooks **from React function components**. Don’t call Hooks from regular JavaScript functions. (There is just one other valid place to call Hooks -- your own custom Hooks. We'll learn about them in a moment.)

We provide a [linter plugin](https://www.npmjs.com/package/eslint-plugin-react-hooks) to enforce these rules automatically. We understand these rules might seem limiting or confusing at first, but they are essential to making Hooks work well.

>Detailed Explanation
>
>You can learn more about these rules on a dedicated page: [Rules of Hooks](/docs/hooks-rules.html).

## 💡 Building Your Own Hooks {#building-your-own-hooks}

Sometimes, we want to reuse some stateful logic between components. Traditionally, there were two popular solutions to this problem: [higher-order components](/docs/higher-order-components.html) and [render props](/docs/render-props.html). Custom Hooks let you do this, but without adding more components to your tree.

Earlier on this page, we introduced a `FriendStatus` component that calls the `useState` and `useEffect` Hooks to subscribe to a friend's online status. Let's say we also want to reuse this subscription logic in another component.

First, we'll extract this logic into a custom Hook called `useFriendStatus`:

```js{3}
import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

It takes `friendID` as an argument, and returns whether our friend is online.

Now we can use it from both components:


```js{2}
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```js{2}
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

The state of these components is completely independent. Hooks are a way to reuse *stateful logic*, not state itself. In fact, each *call* to a Hook has a completely isolated state -- so you can even use the same custom Hook twice in one component.

Custom Hooks are more of a convention than a feature. If a function's name starts with "`use`" and it calls other Hooks, we say it is a custom Hook. The `useSomething` naming convention is how our linter plugin is able to find bugs in the code using Hooks.

You can write custom Hooks that cover a wide range of use cases like form handling, animation, declarative subscriptions, timers, and probably many more we haven't considered. We are excited to see what custom Hooks the React community will come up with.

>Detailed Explanation
>
>You can learn more about custom Hooks on a dedicated page: [Building Your Own Hooks](/docs/hooks-custom.html).

## 🔌 Other Hooks {#other-hooks}

There are a few less commonly used built-in Hooks that you might find useful. For example, [`useContext`](/docs/hooks-reference.html#usecontext) lets you subscribe to React context without introducing nesting:

```js{2,3}
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}
```

And [`useReducer`](/docs/hooks-reference.html#usereducer) lets you manage local state of complex components with a reducer:

```js{2}
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
```

>Detailed Explanation
>
>You can learn more about all the built-in Hooks on a dedicated page: [Hooks API Reference](/docs/hooks-reference.html).

## Next Steps {#next-steps}

Phew, that was fast! If some things didn't quite make sense or you'd like to learn more in detail, you can read the next pages, starting with the [State Hook](/docs/hooks-state.html) documentation.

You can also check out the [Hooks API reference](/docs/hooks-reference.html) and the [Hooks FAQ](/docs/hooks-faq.html).

Finally, don't miss the [introduction page](/docs/hooks-intro.html) which explains *why* we're adding Hooks and how we'll start using them side by side with classes -- without rewriting our apps.
