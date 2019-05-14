# Added SAI_OBJECT_TYPE_BRIDGE_PORT support in ACL

## Description

Below mentioned are some of the sub-items related to this change  
  
- Removing condition in bridge port bridge id attribute (#706) (#707)   
- Fix condition in bridge port bridge id attribute  
- Using validonly flag  
- Add validonly and range tags to Doxygen (#717)  
- add SAI_SWITCH_ATTR_AVAILABLE_ACL_{TABLE,TABLE_GROUP} (#721)  
- add SAI_SWITCH_ATTR_AVAILABLE_NEXT_HOP_GROUP_ENTRY (#722)  
- ACL CRM changes (#731) (#733)  
- Critical Resource Monitoring related changed for ACLs  
- Adapt to new gcc and doxygen (#725)  
- Add return flag in notification data structure. (#745)  
- Add return flag in notification data structure.  
- Update saiqueue.h  
- Update saiserializetest.c  
- Update saivlan.h (#752)    

## Commit  

[Link to the commit](https://github.com/opencomputeproject/SAI/commit/e472cc22654c388e56d749464bde7462bc9d8bda)

## PRs related to this commit

[756](https://github.com/opencomputeproject/SAI/pull/756)  [706](https://github.com/opencomputeproject/SAI/pull/706)  [707](https://github.com/opencomputeproject/SAI/pull/707)  [717](https://github.com/opencomputeproject/SAI/pull/717)  [721](https://github.com/opencomputeproject/SAI/pull/721)  [722](https://github.com/opencomputeproject/SAI/pull/722)  [731](https://github.com/opencomputeproject/SAI/pull/731)  [733](https://github.com/opencomputeproject/SAI/pull/733)  [725](https://github.com/opencomputeproject/SAI/pull/725)  [745](https://github.com/opencomputeproject/SAI/pull/745)  [752](https://github.com/opencomputeproject/SAI/pull/752)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Uninitialize data plane ON removal of switch object

## Description

Below mentioned are some of the sub-items related to this change

- Controls whether data plane is unitialized upon removal of switc object as leaving traffic running without control is not recommended  
- However, on some scenarios, such as fast boot, it is needed to leave the data plane running  
- Host adapter will call this attribute if needed, and then call remove the switch  

## Commit 

[Link to the commit](https://github.com/opencomputeproject/SAI/commit/c26fb2657a7b246548d10458a542ea5fa2d23cd5)

## PRs related to this commit
[795](https://github.com/opencomputeproject/SAI/pull/795)

