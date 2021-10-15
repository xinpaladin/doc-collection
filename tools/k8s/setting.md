# By default this will make sure two pods don't end up on the same node
# Changing this to a region would allow you to spread pods across regions
默认情况下，这将确保两个pods不会出现在同一个节点上
将此更改为一个区域将允许您在整个区域中散布pds
antiAffinityTopologyKey: "kubernetes.io/hostname"