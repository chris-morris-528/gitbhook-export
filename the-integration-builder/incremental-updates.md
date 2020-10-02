# Incremental Updates

After creating and releasing a live plugin, you may later want to introduce new features and functionality with minimal disruption. To do this, you will want to take advantage of the import/export features of the Integration Builder as follows:

1. Export the existing plugin you want to update by scrolling to that plugin in the Integration Builder's list of plugins.
2. Create a new, private plugin in the Builder.
3. Scroll to the bottom of the new plugin, and click Import.
4. Select the exported JSON file from step 1.
5. Once the import completes, you now have a cloned \(but private\) plugin that you can modify.
6. When you've tested your latest changes, you perform the opposite steps by exporting the newer plugin, and importing it into the older plugin.

