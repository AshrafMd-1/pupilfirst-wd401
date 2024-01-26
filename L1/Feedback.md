# Code Review

```ts
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
      // Assign the payload to a variable
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
    console.log('Sign-up successful');

    const data = await response.json();
    // use then clause 
    localStorage.setItem('authToken', data.token);
    localStorage.setItem('userData', JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```

# Fixed Code Review

```ts
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    // Assign the payload to a variable
    const payload = { name: organisationName, user_name: userName, email: userEmail, password: userPassword };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }

    console.log('Sign-up successful');

    // Use const for variable names
    const { token, user } = await response.json();

    localStorage.setItem(authTokenKey, token);
    localStorage.setItem(userDataKey, JSON.stringify(user));

    navigate("/dashboard");

  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};

```

# Iterative Development Phase

![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/46a5429c-8c2f-4eec-a7f9-b168ace659b7)


# Resolving Merge Conflicts

Let's consider a scenario where there is a developer who is pushing a conflicting code snippet to the `develop` branch

**Code Snippet - Develop Branch**

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/users/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ userName: Name, email: Email, password: Password }),
    });

	 if (!response.ok) {
	   throw new Error(`Sign-up failed with status ${response.status}`);
	 }
	 console.log('Sign-up successful');

	 const data = await response.json();
	 localStorage.setItem('authToken', data.token);
	 localStorage.setItem('userData', JSON.stringify(data.user));
	 navigate("/dashboard");
   } catch (error) {
	 console.error('Sign-up failed:', error);
   }
 };
```

**Code Snippet - Developer change**

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/organisations/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ userName: Name, email: Email, password: Password }),
    });

	 if (!response.ok) {
	   throw new Error(`Sign-up failed with status ${response.status}`);
	 }
	 console.log('Sign-up successful');

	 const data = await response.json();
	 localStorage.setItem('authToken', data.token);
	 localStorage.setItem('userData', JSON.stringify(data.user));
	 navigate("/dashboard");
   } catch (error) {
	 console.error('Sign-up failed:', error);
   }
 };
```

Now, let's go through the steps to resolve the merge conflict:

   - Git will tell you of a conflict when you merge the branches.
   - Open the file in your code editor. In this case, let's say the file name is `handleSubmit.js`.
   - You'll see markers telling you where the conflict changes are . It look like `<<<<<<<`, `=======`, and `>>>>>>>`. for example

```ts
 const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
   event.preventDefault();

   try {
   
<<<<<<< HEAD
	 const response = await fetch(`${API_ENDPOINT}/users/signup`, {
=======
	 const response = await fetch(`${API_ENDPOINT}/organisations/signup`, {
>>>>>>> feature/bob-changes

	   method: 'POST',
	   headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ userName: Name, email: Email, password: Password }),
	 });

	 if (!response.ok) {
	   throw new Error(`Sign-up failed with status ${response.status}`);
	 }
	 console.log('Sign-up successful');

	 const data = await response.json();
	 localStorage.setItem('authToken', data.token);
	 localStorage.setItem('userData', JSON.stringify(data.user));
	 navigate("/dashboard");
   } catch (error) {
	 console.error('Sign-up failed:', error);
   }
 };
```

   - Decide what changes to keep or you can combine both. Remove the conflicts and set up the code accordingly. For example:

 ```javascript
 const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
   event.preventDefault();

   try {
	const response = await fetch(`${API_ENDPOINT}/organisations/signup`, {
	method: 'POST',
	headers: { 'Content-Type': 'application/json' },
	body: JSON.stringify({ userName: Name, email: Email, password: Password }),
	  });

	 if (!response.ok) {
	   throw new Error(`Sign-up failed with status ${response.status}`);
	 }
	 console.log('Sign-up successful');

	 const data = await response.json();
	 localStorage.setItem('authToken', data.token);
	 localStorage.setItem('userData', JSON.stringify(data.user));
	 navigate("/dashboard");
   } catch (error) {
	 console.error('Sign-up failed:', error);
   }
 };
```

   - After resolving the conflict, save the file and commit the changes.

     ```bash
     git add handleSubmit.js
     git commit -m "Resolve merge conflict in handleSubmit.js"
     ```

   - Finish the merge process

     ```bash
     git merge --continue
     ```

   - Push the merged changes to the remote repository:

     ```bash
     git push origin develop
     ```

Now, the conflicting changes been successfully resolved.

# CI/CD Integrations

CI CD pipeline is used to automate the testing process this helps when we don't want to manually check some boilerplate testing and only check some edge cases 

### For example 

Here is a file to test this function
> This testing has been copied from ChatGPT 

```js
//jest.js

describe('handleSubmit Function', () => {
  it('submits the form successfully', async () => {
    fetch.mockResolvedValueOnce({ ok: true, json: async () => ({ token: 'mockToken', user: { id: 1, username: 'mockUser' } }) });

    await act(async () => {
      render(<YourComponentContainingForm />);
      await userEvent.type(screen.getByLabelText(/Name/i), 'Alice');
      await userEvent.type(screen.getByLabelText(/Email/i), 'alice@example.com');
      await userEvent.type(screen.getByLabelText(/Password/i), 'password123');
      fireEvent.submit(screen.getByRole('button', { name: /submit/i }));
    });

    expect(fetch).toHaveBeenCalledWith(`${API_ENDPOINT}/organisations/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ userName: 'Alice', email: 'alice@example.com', password: 'password123' }),
    });
    expect(localStorage.setItem).toHaveBeenCalledWith('authToken', 'mockToken');
    expect(localStorage.setItem).toHaveBeenCalledWith('userData', JSON.stringify({ id: 1, username: 'mockUser' }));
    expect(screen.getByText(/Sign-up successful/i)).toBeInTheDocument();
  });
});


```

### The process 

1. At first the PR is pushed
2. Then the pr goes through the automatic testing 
3. If there is an error it displays `error` and you need to recheck your changes 
4. If no error or bug is found it says all `correct` and shows ready to be merged 

**This is how CI/CD pipeline can be useful**
