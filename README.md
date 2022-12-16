# Introduction

This repo reproduces an issue of the [`debug` gem](https://github.com/ruby/debug) as described below.


## Issue Description

Breakpoints added via VS Code seems to be lost after Rails' code reload.

## Steps

1. Clone this repo.
1. Run `$ bundle install`.
1. Run `$ bundle exec bin/rails db:migrate RAILS_ENV=development`.
1. Run `$ bundle exec rdbg -O -n -c -- bundle exec bin/rails s` to run the server with debugger in server mode.
1. Press `F5` to attach VS Code to the debugger.
1. Go to `app/controllers/posts_controller.rb` and place an VS Code breakpoint at line `6`.

    ```rb
    def index
      @posts = Post.all # set the breakpoint at this line
    end
    ```
1. Verify the breakpoint can be hit:
    1. In browser, visit http://localhost:3000.
    1. See the breakpoint being hit.
    1. Press `F5` again to continue the program.
1. Add `puts "foo"` at `app/controllers/posts_controller.rb:6`

    ```rb
    def index
      puts "foo"
      @posts = Post.all
    end
    ```
1. Refresh the page in browser.

## Expected Behaviour

The breakpoint should still be hit.

## Actual Behaviour

The breakpoint would be ignored.

## Other Notes

From DAP server logs, it looks like the debugger does receive updated `setBreakpoints` request:

```
#25986:[>] {"command":"setBreakpoints","arguments":{"source":{"path":"/Users/hung-wulo/src/github.com/st0012/debug-issue/app/controllers/posts_controller.rb","name":"posts_controller.rb","sourceReference":0},"lines":[6],"breakpoints":[{"line":6}],"sourceModified":true},"type":"request","seq":13}
#25986:[<] {"type":"response","command":"setBreakpoints","request_seq":13,"success":true,"message":"Success","body":{"breakpoints":[{"verified":true}]},"seq":977}
```

But the breakpoint wasn't actually added.

