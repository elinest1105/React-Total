# `Custom Hooks` Examples :metal:

[На главную](../README.md)

> #### Несколько примеров кастомных хуков

## Оглавление

- [useBeforeUnload](#use_beforeunload)
- [useClick](#use_click)
- [useEventListener](#use_eventlistener)
- [useFetch](#use_fetch)
- [useHover](#use_hover)
- [useKeyPress](#use_keypress)
- [useLocalStorage](#use_localstorage)
- [useDisableScroll](#use_disablescroll)
- [useOnline](#use_online)
- [useOnScreen](#use_onscreen)
- [usePortal](#use_portal)
- [usePrevious](#use_previous)
- [useRouter](#use_router)
- [useStyle](#use_style)
- [useTheme](#use_theme)
- [useTimer](#use_timer)
- [useWindowSize](#use_windowsize)

## useBeforeUnload <a name="use_beforeunload"></a>

Данный хук позволяет выполнять колбеки перед закрытием (перезагрузкой
) страницы

```js
import { useEffect, useRef } from 'react'

export function useBeforeUnload(fn) {
  const cb = useRef(fn)

  useEffect(() => {
    const onUnload = cb.current
    window.addEventListener('beforeunload', onUnload)
    return () => {
      window.removeEventListener('beforeunload', onUnload)
    }
  }, [cb])
}
```

Более универсальная версия

```js
import { useEffect } from 'react'

export const useBeforeUnload = (value) => {
  const onUnload = (e) => {
    let returnValue
    if (typeof value === 'function') {
      returnValue = value(e)
    } else {
      returnValue = value
    }
    if (returnValue) {
      e.preventDefault()
      e.returnValue = returnValue
    }
    return returnValue
  }

  useEffect(() => {
    window.addEventListener('beforeunload', onUnload)
    return () => window.removeEventListener('beforeunload', onUnload)
    // eslint-disable-next-line
  }, [])
}
```

## useClick <a name="use_click"></a>

Данные хуки позволяют запускать колбеки при клике внутри или снаружи целевого элемента

```js
import { useEffect } from 'react'

export const useClickInside = (ref, cb) => {
  const onClick = ({ target }) => {
    if (ref.current && ref.current.contains(target)) {
      cb()
    }
  }

  useEffect(() => {
    document.addEventListener('click', onClick)
    return () => {
      document.removeEventListener('click', onClick)
    }
  })
}

export const useClickOutside = (ref, cb) => {
  const onClick = ({ target }) => {
    if (ref.current && !ref.current.contains(target)) {
      cb()
    }
  }

  useEffect(() => {
    document.addEventListener('click', onClick)
    return () => {
      document.removeEventListener('click', onClick)
    }
  })
}

// пример использования
// стили, чтобы было красиво
const containerStyles = {
  height: '100vh',
  display: 'flex',
  justifyContent: 'space-evenly',
  alignItems: 'center'
}

const wrapperStyles = {
  display: 'inherit',
  flexDirection: 'column',
  alignItems: 'center'
}

const boxStyles = {
  display: 'grid',
  placeItems: 'center',
  width: '100px',
  height: '100px',
  borderRadius: '4px',
  boxShadow: '0 1px 2px rgba(0,0,0,.3)',
  color: '#f0f0f0',
  userSelect: 'none'
}

const textStyles = {
  userSelect: 'none',
  color: '#3c3c3c'
}

export function App() {
  const insideRef = useRef(null)
  const outsideRef = useRef(null)
  const [insideCount, setInsideCount] = useState(0)
  const [outsideCount, setOutsideCount] = useState(0)

  const insideCb = () => {
    setInsideCount((c) => c + 1)
  }

  const outsideCb = () => {
    setOutsideCount((c) => c + 1)
  }

  useClickInside(insideRef, insideCb)

  useClickOutside(outsideRef, outsideCb)

  return (
    <div style={containerStyles}>
      <div style={wrapperStyles}>
        <div
          style={{ ...boxStyles, background: 'deepskyblue' }}
          ref={insideRef}
        >
          Inside
        </div>
        <p style={textStyles}>Count: {insideCount}</p>
      </div>
      <div style={wrapperStyles}>
        <div
          style={{ ...boxStyles, background: 'mediumseagreen' }}
          ref={outsideRef}
        >
          Outside
        </div>
        <p style={textStyles}>Count: {outsideCount}</p>
      </div>
    </div>
  )
}
```

## useEventListener <a name="use_eventlistener"></a>

Данный хук позволяет регистрировать обработчик событий на целевом элементе

```js
import { useRef, useEffect } from 'react'

export function useEventListener(ev, cb, $ = window) {
  const cbRef = useRef()

  // меняем значение ссылки на колбек при изменении колбека
  useEffect(() => {
    cbRef.current = cb
  }, [cb])

  useEffect(() => {
    const listener = (ev) => cbRef.current(ev)

    $.addEventListener(ev, listener)

    return () => {
      $.removeEventListener(ev, listener)
    }
  }, [ev, $])
}

// пример использования
export function App() {
  const [coords, setCoords] = useState({ x: 0, y: 0 })

  // небольшая оптимизация
  const cb = useCallback(
    ({ clientX, clientY }) => {
      setCoords({ x: clientX, y: clientY })
    },
    [setCoords]
  )

  useEventListener('mousemove', cb)

  const { x, y } = coords

  return (
    <h1>
      Mouse coords: {x}, {y}
    </h1>
  )
}
```

## useFetch <a name="use_fetch"></a>

Хук для выполнения кэшируемых HTTP-запросов с помощью `Fetch API`

```js
import { useState, useRef } from 'react'

export function useFetch(url, options) {
  const [isLoading, setLoading] = useState(true)
  const [response, setResponse] = useState(null)
  const [error, setError] = useState(null)
  const cache = useRef({})

  useEffect(() => {
    if (!url) return

    async function fetchData() {
      if (cache.current[url]) {
        const data = cache.current[url]
        setResponse(data)
      } else {
        try {
          const response = await fetch(url, options)
          const json = await response.json()
          cache.current[url] = json
          setResponse(json)
        } catch (error) {
          setError(error)
        }
      }

      setLoading(false)
    }

    fetchData()
  }, [url])

  return { isLoading, response, error }
}
```

## useHover <a name="use_hover"></a>

Хук для обработки наведения курсора на целевой элемент

```js
import { useState, useEffect, useRef } from 'react'

export function useHover() {
  const [value, setValue] = useState(false)

  const ref = useRef(null)

  const handleMouseOver = () => setValue(true)
  const handleMouseOut = () => setValue(false)

  useEffect(() => {
    const node = ref.current
    if (node) {
      node.addEventListener('mouseover', handleMouseOver)
      node.addEventListener('mouseout', handleMouseOut)
    }

    return () => {
      node.removeEventListener('mouseover', handleMouseOver)
      node.removeEventListener('mouseout', handleMouseOut)
    }
  }, [ref.current])

  return [ref, value]
}

// пример использования
export function App() {
  const [hoverRef, isHovered] = useHover()

  return <div ref={hoverRef}>{isHovered ? '😊' : '☹️'}</div>
}
```

## useKeyPress <a name="use_keypress"></a>

Хук для обработки нажатия клавиш клавиатуры

```js
import { useState, useEffect } from 'react'

export function useKeyPress(target) {
  const [keyPressed, setKeyPressed] = useState(false)

  const onDown = ({ key }) => {
    if (key === target) {
      setKeyPressed(true)
    }
  }

  const onUp = ({ key }) => {
    if (key === target) {
      setKeyPressed(false)
    }
  }

  useEffect(() => {
    window.addEventListener('keydown', onDown)
    window.addEventListener('keyup', onUp)

    return () => {
      window.removeEventListener('keydown', onDown)
      window.removeEventListener('keyup', onUp)
    }
  // eslint-disable-next-line
  }, [])

  return keyPressed
}

// пример использования
function App() {
  const happy = useKeyPress('h')
  const sad = useKeyPress('s')

  return (
    <>
      <div>h, s</div>
      <div>
        {happy && '😊'}
        {sad && '😢'}
      </div>
    </>
  )
}
```

## useLocalStorage <a name="use_localstorage"></a>

Хук для получения и записи значений в локальное хранилище

```js
import { useState, useEffect } from 'react'

export function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const item = window.localStorage.getItem(key)
    return item ? JSON.parse(item) : initialValue
  })

  useEffect(() => {
    const item = JSON.stringify(value)
    window.localStorage.setItem(key, item)
    // eslint-disable-next-line
  }, [value])

  return [value, setValue]
}
```

## useDisableScroll <a name="use_disablescroll"></a>

Хук для отключения прокрутки страницы, например, при вызове модального окна

```js
import { useLayoutEffect } from 'react'
// другой кастомный хук, см. ниже
import { useStyle } from './useStyle'

export function useDisableScroll() {
  const [, setOverflow] = useStyle('overflow')

  useLayoutEffect(() => {
    setOverflow('hidden')

    return () => {
      setOverflow('auto')
    }
  }, [])
}
```

## useOnline <a name="use_online"></a>

Хук для определения статуса пользователя

```js
import { useState, useEffect } from 'react'

const getStatus = () =>
  typeof navigator !== 'undefined' && typeof navigator.onLine === 'boolean'
    ? navigator.onLine
    : true

export const useOnline = () => {
  const [status, setStatus] = useState(getStatus())

  const setOnline = () => setStatus(true)
  const setOffline = () => setStatus(false)

  useEffect(() => {
    window.addEventListener('online', setOnline)
    window.addEventListener('offline', setOffline)

    return () => {
      window.removeEventListener('online', setOnline)
      window.removeEventListener('offline', setOffline)
    }
  }, [])

  return status
}
```

## useOnScreen <a name="use_onscreen"></a>

Хук для определения отображения элемента на экране

```js
import { useEffect } from 'react'

export const useOnScreen = (ref, margin = '0px') => {
  const [isIntersecting, setIntersecting] = useState(false)

  useEffect(() => {
    const O = new IntersectionObserver(
      ([entry]) => {
        setIntersecting(entry.isIntersecting)
      },
      { margin }
    )
    if (ref.current) {
      O.observe(ref.current)
    }
    return () => {
      O.unobserve(ref.current)
    }
  }, [])
  return isIntersecting
}
```

## usePortal <a name="use_portal"></a>

Хук для создания порталов

```js
import { useRef, useEffect } from 'react'

function createRoot(id) {
  const root = document.createElement('div')
  root.setAttribute('id', id)
  return root
}

function addRoot(root) {
  document.body.insertAdjacentElement('beforeend', root)
}

export function usePortal(id) {
  const rootRef = useRef(null)

  useEffect(
    function setupElement() {
      const existingParent = document.getElementById(id)
      const parent = existingParent || createRoot(id)

      if (!existingParent) {
        addRoot(parent)
      }

      parent.appendChild(rootRef.current)

      return function removeElement() {
        rootRef.current.remove()
        if (!parent.childElementCount) {
          parent.remove()
        }
      }
    },
    [id]
  )

  function getRoot() {
    if (!rootRef.current) {
      rootRef.current = document.createElement('div')
    }
    return rootRef.current
  }

  return getRoot()
}
```

## usePrevious <a name="use_previous"></a>

Хук для сохранения значения из предыдущего рендеринга

```js
import { useEffect, useRef } from 'react'

export const usePrevious = (val) => {
  const ref = useRef()
  useEffect(() => {
    ref.current = val
  })
  return ref.current
}
```

## useRouter <a name="use_router"></a>

Хук, объединяющий в себе функционал всех хуков `React Router DOM`

```js
import { useMemo } from 'react'
import {
  useHistory,
  useLocation,
  useParams,
  useRouteMatch
} from 'react-router-dom'
import queryString from 'query-string'

export const useRouter = () => {
  const history = useHistory()
  const location = useLocation()
  const params = useParams()
  const match = useRouteMatch()

  return useMemo(
    () => ({
      push: history.push,
      replace: history.replace,
      pathname: location.pathname,
      query: {
        ...queryString.parse(location.search),
        ...params
      },
      history,
      location,
      match
    }),
    [history, location, match, params]
  )
}
```

## useStyle <a name="use_style"></a>

Хук для получения и изменения стилей целевого элемента

```js
import { useState, useEffect } from 'react'

export function useStyle(prop, $ = document.body) {
  const [value, setValue] = useState(getComputedStyle($).getPropertyValue(prop))

  useEffect(() => {
    $.style.setProperty(prop, value)
  }, [value])

  return [value, setValue]
}

// пример использования
export function App() {
  // другой кастомный хук, см. ниже
  const { width, height } = useWindowSize()
  const [color, setColor] = useStyle('color')
  const [fontSize, setFontSize] = useStyle('font-size')

  // имитация медиа запросов
  useEffect(() => {
    if (width > 1024) {
      setColor('green')
      setFontSize('2em')
    } else if (width > 768) {
      setColor('blue')
      setFontSize('1.5em')
    } else {
      setColor('red')
      setFontSize('1em')
    }
  }, [width])

  return (
    <>
      <h1>
        Window size: {width}, {height}
      </h1>
      <h2>Color: {color}</h2>
      <h3>Font size: {fontSize}</h3>
    </>
  )
}
```

## useTheme <a name="use_theme"></a>

Хук для установки темы оформления страницы

```js
import { useLayoutEffect } from 'react'

export function useTheme(theme) {
  useLayoutEffect(() => {
    for (const [prop, val] in theme) {
      document.documentElement.style.setProperty(`--${prop}`, val)
    }
  }, [theme])
}
```

## useTimer <a name="use_timer"></a>

Хуки-обертки для `setTimeout()` и `setInterval()`

```js
import { useEffect, useRef } from 'react'

export function useTimeout(cb, ms) {
  const cbRef = useRef()

  useEffect(() => {
    cbRef.current = cb
  }, cb)

  useEffect(() => {
    function tick() {
      cbRef.current()
    }
    if (ms > 1) {
      const id = setTimeout(tick, ms)
      return () => {
        clearTimeout(id)
      }
    }
  }, [ms])
}

export function useInterval(cb, ms) {
  const cbRef = useRef()

  useEffect(() => {
    cbRef.current = cb
  }, cb)

  useEffect(() => {
    function tick() {
      cbRef.current()
    }
    if (ms > 1) {
      const id = setInterval(tick, ms)
      return () => {
        clearInterval(id)
      }
    }
  }, [ms])
}
```

## useWindowSize <a name="use_windowsize"></a>

Хук для получение размеров области просмотра

```js
import { useState, useEffect } from 'react'

export function useWindowSize() {
  const [size, setSize] = useState({})

  useEffect(() => {
    function onResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      })
    }

    window.addEventListener('resize', onResize)

    onResize()

    return () => window.removeEventListener('resize', onResize)
  }, [])

  return size
}

// пример использования
export function App() {
  const { width, height } = useWindowSize()
  // другой кастомный хук
  const [color, setColor] = useStyle('color')
  const [fontSize, setFontSize] = useStyle('font-size')

  useEffect(() => {
    if (width > 1024) {
      setColor('green')
      setFontSize('2em')
    } else if (width > 768) {
      setColor('blue')
      setFontSize('1.5em')
    } else {
      setColor('red')
      setFontSize('1em')
    }
  }, [width])

  return (
    <>
      <h1>
        Window size: {width}, {height}
      </h1>
      <h2>Color: {color}</h2>
      <h3>Font size: {fontSize}</h3>
    </>
  )
}
```