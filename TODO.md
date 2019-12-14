### TODO
* DONE: Analzye what should be able to be passed to ansible-playbook from an execution item
* DONE: assert that each execution item looks correct
* DONE: Allow passing of all applicable things from an execution item
* DONE: Integrate the execution schema tests with new schema
* DONE: condense templating logic for playbook command generation and tests down to macros/functions
* DONE: assert that each dependency item looks correct
* DONE: add version number assertion: version number needs to be something that can be determined as newer or older, otherwise, it would be a pointless versioning system... meaning, if you can't tell where a particular version falls within all the versions, then you can't tell which is the latest version, or when changes occurred etc. So the version value needs to be a proper value, that can be mathmatically assessed
   - this brings up a good point... so with regards to git version, currently we're passing the version as the git version, which will not work in the above statement. So git version needs to be apart of the src key... which then leads to that probably needing to be a dictionary now... which means schema change, and test change :-)
* DONE: Integrate the dependency assertion into role and the new dependency schema
* DONE: switch git version to something on src
* DONE: by default, fail dependency collection if another dependency has the same name. allow user to disable this. OR consider that if it's the same name, choose the newer of the 2.
    - THIS ONE, because we have to consider that includes/excludes may be different across duplicates, and therefore we don't want to process the paths until we choose the correct one >>>> May want to download/process all the dependencies before processing the paths, because if we have a duplicate name, we can just process the path for the newer of the 2
    - OORRR process the path as we go... and then with each dependency, check if another dependency has the same name and type, then assess if the current dependency version is newer or older, and if older then replace with the current(which would always be newer)
* DONE: change to collect, filter, assemble
      collect: find, get
      filter:
      assemble: path
  <!-- - get, select, assemble
      get: find, collect
      select: select
      assemble: path

  - collect, select, assemble
      collect: find, get
      select: select
      assemble: path -->

  <!-- - find, collect, process -->

* DONE: Integrate the execution assertion into role and the new execution schema
* DONE: Integrate the execution playbook command builder into role
* DONE: setup some examples that run a little longer
* DONE: activate the output loop
* DONE: fix the quoting stuff for the command builder. shouldn't have to replace " with \" in json transform
    - DONE: switch to argv for command module
    - DONE: test different inputs to see how resilient it is. like adding quotes in environment vars and extra vars
* DONE: configure the diy output
    - DONE: default to aliases
    - DONE: switch to enable default callback output
        - just have to pass: -e "{ ansible_callback_diy_all_default: '{{ omit }}' }"
    - DONE: get rid of Script started/done messages on output
    - DONE: show failures by default
    - DONE: print invocation label
    - DONE: print initialization started for execution
    - DONE: print initialization completed for execution
    - DONE: indicate which execution the output is for
    - DONE: indicate the dependencies installed?
    - DONE: indicate the start/completion of an execution? no, because it's just not necessary. you can see the start and finish just by looking at the "output" lines
    - DONE: add some color to make things easier to see
        - DONE: execution
        - DONE: dependencies assembled
    - DONE: indicate if an execution was successfull or failed?
        - maybe something like "one or more executions failed" vs "all executions successful"
        - or "failed executions: blah, blah" vs "all executions successful"
        - or
            - execution 0: successful
            - execution 1: failed
            - execution 2: successful
    - DONE: indicate duration of execution, including start and finish timestamps
* DONE: test some executions
   - DONE: test with just 1 execution
   - DONE: test with multiple
     - DONE: also probably need to be using set_fact to initialize default vars instead of defaults/main
        - DONE: dependency manager
            - just resetting vars that were set using "set_fact" module because they exist in the session scope instead of just the include scope
        - DONE: execution manager
            - same as dep manager.
            - last color index should not be reset
     - DONE: need to go back and probably make sure that we're tracking everything in the execution's dict (like label, output prefix etc.).
        - add callback_output_prefix to execution dict
     - DONE: double check that dependencies look correct

* DONE: remove invocation label print and wait. it's not necessary as the invocation lable is on every line being printed. no one is going to right click, scroll up, and copy it when they could just get it on the line just printed. they just need to know where it's being printed on each line. i.e. documentation solves this.

* DONE: don't output when there are only blank lines

* DONE: may need to add a page break/seperator of some kind between execution outputs
    -  if you only have 1 execution running, it's going to break up the output in an unclean way. also, if you increase the refresh rate, it's really going to make it look unclean...
    - IDEA!: instead of breaking across the whole page, we could just do a small horizontal line on the left (and maybe right) on the 1st line and last line of the output. this will slightly divide the page for your eye, without cluttering the output. Now that i think about it, we can just slightly tweak the prefix_lines macro to be an affix macro instead, and give it logic to prefix and suffix(infix aswell if we want to be fully semantically accurate with the macro name, but we don't need to infix right now.)
        - actually, how above just the pipe in the same color as the execution color. then we don't have to use unicode characters, and should look clean

* DONE: execution label as an optional way of labeling an execution
* DONE: timestamp on output
    - just have to pass: -e "{ output_timestamps: true }"
* DONE: fix color formatting regex
* DONE: fix phase alignment
* DONE: switch to >= 2 space delimited

* DONE: fix execution id
* DONE: kill invocation label + execution index number equals killing a specific execution within invocation
    - example: `ansible-playbook -i controller.yml -e "{ filter: 123123.1 }" kill.yml`
* DONE: kill invocation label + execution label equals killing a specific execution within invocation
    - example: `ansible-playbook -i controller.yml -e "{ filter: 123123.blah }" kill.yml`
* DONE: kill invocation label + execution label matching multiple executions equals killing executions with that label within invocation
    - example assuming blah label was attached to multiple executions: `ansible-playbook -i controller.yml -e "{ filter: 123123.blah }" kill.yml`
* DONE: kill invocation label + execution label + execution index number equals killing execution with execution label and index within invocation
    - example: `ansible-playbook -i controller.yml -e "{ filter: 123123.blah.1 }" kill.yml`
* DONE: kill invocation id equals kill all executions within invocation
    - example: `ansible-playbook -i controller.yml -e "{ filter: 123123 }" kill.yml`
* DONE: test killing some executions

* DONE: execution logging
    * tool's logging:
        - redirect output to log file: `ansible-playbook -i controller.yml -e "@examples/executions.yml" -e "{ invocation_label: 123123 }" execute.yml &> execution.log`
        - output to console and write to log file: `ansible-playbook -i controller.yml -e "@examples/executions.yml" -e "{ invocation_label: 123123 }" execute.yml 2>&1 | tee execution.log`
        - use built in ansible log functionality: `ANSIBLE_LOG_PATH=execution.log ansible-playbook -i controller.yml -e "@examples/executions.yml" -e "{ invocation_label: 123123 }" execute.yml`
    * execution's logging:
        - use tool's logging (execution logs are part of tool's logs)
        - use built in ansible log functionality via env var:
        ```yaml
        - label: blah
          playbooks:
            - playbook.yml
          inventory:
            - inventory
          environment:
            ANSIBLE_LOG_PATH: path/to/execution.log #relative to execution space
            ...
            ANSIBLE_LOG_PATH: /tmp/logs/execution.log #full path
            ...
        ```

* DONE: output execution space path

* DONE: git folder cleanup option on a per execution basis
    - instead, maybe just an option to clean dependency manager working directory after dependency manager finishes. because i'm not really sure cleanup of the working directory needs to be controlled on a super granular level. basically, if the dependency manager doesn't work correctly, and i want to debug, i want to see all the items it's producing, in which case i would disable the cleanup option, so the next time it runs, the working directory in it's entirety is left alone, which should allow me to see what it got, what it packaged etc.
        - disable cleanup: `-e "{ cleanup: false }"`
        - enable cleanup(default): `-e "{ cleanup: false }"`

* DONE: add ansible_inventory type default include and exclude patterns

* WIP: fill out README
* add examples
    - DONE: create test playbook repo
    - DONE: create test inventory 1 repo
    - DONE: create test role 1 repo
    - DONE: create test role 2 repo
* create execute repo
* setup in travis-ci
* add CHANGELOG
* DONE: add License

can do later:
* cleanup execution space
* finish dependency manager types
