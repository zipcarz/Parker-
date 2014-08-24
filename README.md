Parker-
=======
  */
28	28	 public class GPUFragment extends PreferenceFragment {
29	29	 
30		-    public static final String GPU_FREQ_MAX = "/sys/kernel/gpu_control/max_freq";
31	30	     public static final String GPU_CONTROL_ACTIVE = "/sys/kernel/gpu_control/gpu_control_active";
32	31	     public static final String GPU_FREQ_CUR = "/proc/gpu/cur_rate";
33	32	     public static final String DISPLAY_COLOR ="/sys/class/misc/display_control/display_brightness_value";
34		-    public static final String GPU_FREQ_NEXUS4 = "/sys/class/kgsl/kgsl-3d0/max_gpuclk";
35	33	     public static final String GPU_FREQ_NEXUS4_VALUES = "/sys/class/kgsl/kgsl-3d0/gpu_available_frequencies";
34	+    public static final String[] GPU_FILES = {"/sys/kernel/gpu_control/max_freq", /* Defy 2.6 Kernel */
35	+                                            "/sys/class/kgsl/kgsl-3d0/max_gpuclk", /* Adreno GPUs */
36	+                                            "/sys/devices/platform/omap/pvrsrvkm.0/sgx_fck_rate" /* Defy 3.0 Kernel */};
36	37	     public static final String SWEEP2WAKE = "/sys/android_touch/sweep2wake";
37	38	     public static final String COLOR_CONTROL = "/sys/devices/platform/kcal_ctrl.0/kcal";
38	39	     public boolean checkGpuControl;
...	...	@@ -60,24 +61,26 @@ public void onCreate(Bundle savedInstanceState) {
60	61	         final ListPreference display_control = (ListPreference)root.findPreference("display_control");
61	62	         final Preference color_control = root.findPreference("display_color_control");
62	63	 
64	+        /* Find correct gpu path */
65	+        for (String a : GPU_FILES) {
66	+            if (new File(a).exists()) {
67	+                gpu_file = a;
68	+                break;
69	+            }
70	+        }
71	+
63	72	         if(!(new File(SWEEP2WAKE).exists()))
64	73	             gpuCategory.removePreference(sweep2wake);
65	74	 
66	75	         if(!(new File(GPU_CONTROL_ACTIVE).exists()))
67	76	                 gpuCategory.removePreference(gpu_control_enable);
68	77	 
69		-        if (!(new File(GPU_FREQ_MAX).exists() || new File(GPU_FREQ_NEXUS4).exists()))
78	+        if (gpu_file == null)
70	79	             gpuCategory.removePreference(gpu_control_frequencies);
71	80	 
72	81	         if (!(new File(COLOR_CONTROL).exists()))
73	82	             gpuCategory.removePreference(color_control);
74	83	 
75		-        // Check for nexus;
76		-        if (new File(GPU_FREQ_NEXUS4).exists())
77		-            gpu_file = GPU_FREQ_NEXUS4;
78		-        else
79		-            gpu_file = GPU_FREQ_MAX;
80		-
81	84	         if (AeroActivity.shell.getInfo(DISPLAY_COLOR).equals("Unavailable"))
82	85	             gpuCategory.removePreference(display_control);
83	86	 
...	...	@@ -111,7 +114,7 @@ public boolean onPreferenceClick(Preference preference) {
111	114	         display_control.setEntryValues(display_values);
112	115	 
113	116	         // Just throw in our frequencies;
114		-        if (gpu_file.equals(GPU_FREQ_NEXUS4)) {
117	+        if (new File(GPU_FREQ_NEXUS4_VALUES).exists()) {
115	118	             gpu_control_frequencies.setEntries(AeroActivity.shell.getInfoArray(GPU_FREQ_NEXUS4_VALUES, 1, 0));
116	119	             gpu_control_frequencies.setEntryValues(AeroActivity.shell.getInfoArray(GPU_FREQ_NEXUS4_VALUES, 0, 0));
117	120	         } else {
