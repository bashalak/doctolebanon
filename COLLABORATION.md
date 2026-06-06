# Working together on DoctoLebanon

A short guide for collaborating without stepping on each other. (For Baker + teammates.)

---

## 1. Inviting someone

The repo is **private**, so a teammate needs a free GitHub account, then gets added:

1. Repo → **Settings** → **Collaborators** (left menu).
2. **Add people** → their GitHub **username or email** → **Add**.
3. They accept the email/notification invite.

They put the code on their computer (Git must be installed):
```
git clone https://github.com/bashalak/doctolebanon.git
```

---

## 2. The daily routine (do this every session)

```
git pull          # 1. get the latest changes BEFORE you start
# ...do your work...
git add .         # 2. stage your changes
git commit -m "Clear message about what you did"   # 3. save a snapshot
git pull          # 4. get any changes your teammate pushed while you worked
git push          # 5. upload yours
```
The two `git pull`s (before starting and before pushing) prevent most conflicts.

---

## 3. The 5 golden rules

1. **Pull before you start.**
2. **Divide the work** — don't both edit the same file at the same time.
3. **Commit small and often**, with clear messages.
4. **Pull again right before you push.**
5. **Communicate** — say what you're working on today.

---

## 4. The "proper" way — branches + Pull Requests

Each person works in their own branch, then merges through a reviewed Pull Request. `main` always stays clean & deployable.

```
git checkout -b my-feature      # create + switch to your own branch
# ...work...
git add .
git commit -m "What I changed"
git push -u origin my-feature   # upload the branch
```
Then on GitHub: **Compare & pull request** → review → **Merge** into `main`.
Everyone else updates with:
```
git checkout main
git pull
```

> Netlify auto-deploys `main`, so only merged, reviewed work goes live.

---

## 5. If you hit a merge conflict (it's normal!)

Git marks the clash like this inside the file:
```
<<<<<<< HEAD
your version
=======
their version
>>>>>>> their-branch
```
Edit the file: keep the correct combination, delete the `<<<<<<<`, `=======`, `>>>>>>>` lines, then:
```
git add .
git commit -m "Resolve conflict"
git push
```
Your editor highlights conflicts and offers "Accept Current / Incoming / Both" buttons. And Claude can resolve them with you — just ask.

---

## 6. Tip specific to this project
Right now all the code is in one file (`index.html`) — the easiest thing to conflict on. When we reach **Stage C** we'll split it into multiple files, which makes parallel work much safer.
