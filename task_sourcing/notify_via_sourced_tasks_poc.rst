=====================================
Notifying Handlers from Sourced Tasks
=====================================

.. contents::
   :local:

Task Keyword on Static ``import_tasks``
=======================================

*  In ansible 2.9, the ``notify`` keyword has no effect::

      PLAY [illustrate differences in static/dynamic task sourcing] *********************************************************

      TASK [imported task] **************************************************************************************************
      changed: [localhost] => {
          "msg": "this is the imported \"static\" task"
      }

      TASK [dynamic "include" source] ***************************************************************************************
      included: /home/billc/src/ansible_pocs/task_sourcing/included.yml for localhost

      TASK [included task] **************************************************************************************************
      changed: [localhost] => {
          "msg": "this is the included \"dynamic\" task"
      }

      PLAY RECAP ************************************************************************************************************
      localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

*  In ansible 2.11, the ``notify`` keyword will be applied to each sourced task::

      PLAY [illustrate differences in static/dynamic task sourcing] *********************************************************

      TASK [imported task] **************************************************************************************************
      changed: [localhost] => {
          "msg": "this is the imported \"static\" task"
      }

      TASK [dynamic "include" source] ***************************************************************************************
      included: /home/billc/src/ansible_pocs/task_sourcing/included.yml for localhost

      TASK [included task] **************************************************************************************************
      changed: [localhost] => {
          "msg": "this is the included \"dynamic\" task"
      }

      RUNNING HANDLER [static handler] **************************************************************************************
      ok: [localhost] => {
          "msg": "this is the import/static handler"
      }

      RUNNING HANDLER [dynamic handler] *************************************************************************************
      ok: [localhost] => {
          "msg": "this is the include/dynamic handler"
      }

      PLAY RECAP ************************************************************************************************************
      localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Dynamic ``include_tasks``
=========================

Task Keyword
------------

* This applies only to the sourcing task itself

Module ``apply:`` Option
------------------------

*  In ansible 2.9, the ``notify`` value results in a task syntax error::

      PLAY [illustrate differences in static/dynamic task sourcing] *********************************************************

      TASK [imported task] **************************************************************************************************
      changed: [localhost] => {
          "msg": "this is the imported \"static\" task"
      }

      TASK [dynamic "include" source] ***************************************************************************************
      fatal: [localhost]: FAILED! => {"reason": "'notify' is not a valid attribute for a Block\n\nThe error appears to be in '/home/billc/src/ansible_pocs/task_sourcing/pb.yml': line 21, column 17, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n            apply:\n                tags:\n                ^ here\n"}

      PLAY RECAP ************************************************************************************************************
      localhost                  : ok=1    changed=1    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

   Reason::

      'notify' is not a valid attribute for a Block

      The error appears to be in 'task_sourcing/pb.yml': line 21, column 17, but may
      be elsewhere in the file depending on the exact syntax problem.

      The offending line appears to be:

                  apply:
                      tags:
                      ^ here

*  In ansible 2.11, the ``notify`` value applies to each sourced task
