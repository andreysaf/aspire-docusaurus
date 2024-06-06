# Website

This website is built using [Docusaurus](https://docusaurus.io/), a modern static website generator.

It is deployed with github pages and live at [https://andreysaf.github.io/aspire-docusaurus](https://andreysaf.github.io/aspire-docusaurus).

### Prerequisites

- [Node](https://nodejs.org/en)
- [Github Desktop](https://desktop.github.com/)
- [Visual Studio Code](https://code.visualstudio.com/)

### Installation

```
$ npm i
```

### Local Development

```
$ npm start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

```
$ npm build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

### Deployment

```
$ GIT_USER=<Your GitHub username> yarn deploy
```

If you are using GitHub pages for hosting, this command is a convenient way to build the website and push to the `gh-pages` branch.

### Adding Authentication

You can protect just certain paths or the whole documentation site. This example uses [Supabase](https://supabase.com/) for authentication purposes. Create a project in [Supabase](https://supabase.com/) to get your project URL and API KEY.

#### Step 1: Add Supabase Component

Install `@supabase/auth-ui-react`, `@supabase/auth-ui-shared`, `@supabase/supabase-js` by running in your terminal:

```
npm i @supabase/auth-ui-react @supabase/auth-ui-shared @supabase/supabase-js
```

Create a new component `src/components/AuthSupabase/index.js`:

```javascript
import { createClient } from '@supabase/supabase-js';
export const supabase = createClient('https://YOUR_APP_PATH.supabase.co', 'YOUR_API_KEY');
```

#### Step 2: Restrict Path

Create a new component under `src/theme/Root.js`:

```javascript
import React, { useState, useEffect } from 'react';
import { supabase } from '@site/src/components/AuthSupabase';
import { Auth } from '@supabase/auth-ui-react';
import { ThemeSupa } from '@supabase/auth-ui-shared';


// Default implementation, that you can customize
export default function Root({ children }) {

    const [session, setSession] = useState(null)

    useEffect(() => {
        supabase.auth.getSession().then(({ data: { session } }) => {
            setSession(session)
        })

        const {
            data: { subscription },
        } = supabase.auth.onAuthStateChange((_event, session) => {
            setSession(session)
        })

        return () => subscription.unsubscribe()
    }, [])

    if (!session) {
        return (<div style={{display: "flex", flexWrap: "wrap",
            textAlign: "center", justifyContent: "center"
        }}>
            <Auth supabaseClient={supabase} providers={[]} appearance={{ theme: ThemeSupa }} />
        </div>)
    }
    else {
        return (<>{children}</>)
    }
}
```

You can restrict just a certain path by swizzling other components. Learn more about swizzling [here](https://docusaurus.io/docs/swizzling/).

#### Step 3: Add Sign Out Button

Create a new file in `src/theme/Navbar/ColorModeToggle/index.js`:

```javascript
import React from 'react';
import ColorModeToggle from '@theme-original/Navbar/ColorModeToggle';
import { supabase } from '@site/src/components/AuthSupabase';

export default function ColorModeToggleWrapper(props) {

  function signOut() {
    supabase.auth.signOut();
  }

  return (
    <>
    <a style={{marginRight: 15, cursor: "pointer", color: '#222222'}} onClick={signOut}>
        Logout
      </a>
      <ColorModeToggle {...props} />
    </>
  );
}

```

### Helpful Links

- [Convert PDF to MD](https://pdf2md.morethan.io/)
- [Authentication](https://supabase.com/)
- [Advanced Search](https://www.algolia.com/)
- [Chatbot](https://docsbot.ai/)


