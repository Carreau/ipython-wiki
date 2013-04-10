Autosave in the IPython Notebook

This is a proposal for simple autosave in the IPython Notebook.

There are two basic notions of autosave:

1. old desktop-style: autosave only creates backups, user action is the only way to create a 'real' save, but can restore from autosave if newer than last 'real' save (emacs, MS Word, etc.).
2. modern / webapp-style: autosave is *real* save, but user may explicitly save a checkpoint or version, which will can be restored later (All webapps, really, OS X Versions, etc.).

This proposal is an implementation of the latter style,
which is more appropriate for a webapp like the notebook.

## Autosaving

The notebook should be automatically saved periodically.
These are real, regular saves, overwriting the file on the filesystem,
exactly as save is implemented today.

## Checkpointing

The user can manually 'save a version' that will never be clobbered by autosave.
In a primitive file-based implementation,
this amounts to a regular save, plus a copy to another location.
To avoid implementing a full VCS, a simple implementation would only support one user-specified checkpoint.
The checkpoint should be in a location that is discoverable from the notebook,
and consistent across notebook server sessions (i.e. keyed by notebook_id will not work for FileNB).

If we ever implement multiple checkpoints and history navigation,
this should probably just use git, and require that the project be a repo.

## Reverting to checkpoint

There will only be one checkpoint, and the user should have the option of reverting
the notebook state to the checkpoint.

A revert does:

- load the notebook from the checkpoint
- write it to the 'canonical' location
- leaves the checkpoint file untouched


## Deleting checkpoints

In addition to creating checkpoints, we may want a mechanism to delete checkpoints.

## Issues

- How do we choose autosave frequency?  Should it be a function of how long saves take
  (in case of large notebooks and/or slow connections)?
- should we trigger autosave on particular events, such as finishing the output of a given cell,
  rather than just a timer?

## Why not autosave backups?

The first / old-fashioned approach is more common in some contexts (particularly desktop editors).
It has the advantage of not changing the traditional save model *at all*,
only adding autosaved backups to protect against crash, etc.
I would argue that this is the wrong approach for a web-based application,
where *true* autosave is generally expected.
Having autosaved backups also makes loading a document much more complicated,
because the following logic must be followed every time a notebook is opened:

- does the notebook have an autosaved backup?
  - yes, is the backup newer than the manual saved version?
    - yes - prompt the user: "Load autosaved backup (DATE), newer than last save (DATE)?"
      - autosaved - copy autosave to real location, delete autosave
      - regular - delete autosave
    - no - delete the autosave, it shouldn't have existed in the first place

which is particularly cumbersome in a webapp, where this requires multiple HTTP requests,
and can become inconsistent if there are multiple clients viewing the same file.

Note that when you have multiple viewers of a notebook, this prompt would actually be expected to appear almost *every time* someone opens the notebook.

Compare with the proposed implementation,
where it is unambiguous which file should be opened at load time,
as revert-to-checkpoint is an explicit user action.

It is also much more difficult to have a clean 'unsaved changes, close without saving?'
dialog in a webapp than a desktop app,
so we would be much more likely to have autosaved backups that shouldn't exist littering the filesystem, which is gross.

I also think it is better to encourage users to explicitly decide to "remember this state",
rather than continue to enforce the old model,
where it is assumed that the document you are editing does not actually exist,
and it will go away if you just close it without saving.

## A  better approach

The model I have in my head of autosave is along the lines of [OS X Versions](http://support.apple.com/kb/ht4753),
implemented via git.
I do not believe it is appropriate for IPython (at least not by default),
but I will include a description here anyway.

1. every autosave is a regular filesystem save
2. checkpoint does `git add notebook.ipynb && git commit -m 'notebook checkpoint'` (OR just `git add notebook.ipynb`)
3. revert gives a view of the history of the file in git, and checks out with `git checkout <hash> notebook.ipynb`

The reasons not to do this in IPython (by default, anyway):

1. we don't want the notebook to depend on git (or any other VCS)
2. I wouldn't want to support any VCS other than git
3. creating a checkpoint when a commit may be in the middle of staging could be messy

# Implementation

## Python Implementation

NotebookManager objects add the following methods:

- `create_checkpoint(notebook_id)` - creates a checkpoint for the current state of the notebook and returns the `checkpoint_id`
- `restore_checkpoint(notebook_id, checkpoint_id)` - restores a notebook to the state of a given `checkpoint_id`
- `delete_checkpoint(notebook_id, checkpoint_id)` - deletes a particular checkpoint for a given notebook
- `list_checkpoints(notebook_id)` - list checkpoints for a given notebook.

### Questions remaining

- What should `list_checkpoints` return?  A list of checkpoint IDs? A list of dicts with checkpoint information? If a dict, what sort of information? Presumably at least last-modified date.

## URL scheme

The following URLs are added to the notebook server:

| HTTP verb | URI | Action |
|:---:|:---:|:---|
| `GET` | /notebooks/ **notebook-id** /checkpoints | return checkpoint **list** as JSON |
| `POST` | /notebooks/ **notebook-id** /checkpoints | **create** new checkpoint, return new checkpoint ID |
| `POST` | /notebooks/ **notebook-id** /checkpoints/ **checkpoint-id** | **restore** the notebook to checkpoint_id |
| `DELETE` | /notebooks/ **notebook-id** /checkpoints/ **checkpoint-id** | **delete** checkpoint `checkpoint-id` |

### URL questions

- Should **restore** / **delete** be at `/notebooks/notebook-id/checkpoints/checkpoint-id`, or just at `/notebooks/notebook-id/checkpoints`, taking checkpoint-id as a request parameter?
- In general, all **create** calls should be preceded by a **save**, and all **restores** should be followed by a **load**.  Should this be included in the requests themselves, such that a `create` request results in a save and create action on the server, and a **restore** request results in a restore and a load action?  Or should these actions be kept separate, so that every save&checkpoint and restore&load is actually two HTTP requests?

## UI changes

We currently have the following UI for save:

- cmd/ctrl-s
- ctrl-M-s
- File Menu / Save
- Menu Bar / Save Button

I am not 100% clear on what the UI should be for creating a checkpoint versus plain save.  Should *all* user-initiated saves be checkpoints?  Or should plain save be kept in most of the places it currently resides? Or somewhere in-between?

My current thought:

- cmd/ctrl-s: **plain save**
- ctrl-M-s: **checkpoint**
- File / Save: **plain save**
- *new* File / Save Checkpoint: **checkpoint**
- Menu Bar / Save Button: **checkpoint**

That is, most user-initiated saves are checkpoints, excluding the unconscious reflect cmd-S, and the unambiguous File Menu choices, where both are next to each other.

100% of user-initiated saves being checkpoints is perhaps the cleanest, though.