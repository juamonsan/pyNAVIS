**************************************************
Convert from mono to stereo files and vice versa
**************************************************

From mono to stereo
###################

To convert from a mono SpikesFile to a stereo SpikesFile, first load the mono file:

.. prompt:: python \

    from pyNAVIS import *
    settings = MainSettings(num_channels=16, mono_stereo=0, on_off_both=1, address_size=2, ts_tick=0.2, bin_size=10000)
    mono_file = Loaders.loadAEDAT('path/to/file/name.aedat', settings)

.. warning::
    Pay attention to the **mono_stereo** parameter, which was set to 0, meaning that the file that is loaded is mono.

Then execute the ``mono_to_stereo()`` function:

.. prompt:: python \

    stereo_file = Functions.mono_to_stereo(mono_file, delay=0, settings=settings, return_save_both=0)
    
Where **delay** is the time delay between left and right information.

.. note::
    
    When **return_save_both** is set to 0, the information will be returned as a SpikesFile. If it is set to 1, the information will be saved instead of being returned. If it is set to 2, the information will be returned and also saved.

    If **return_save_both** is set to either 1 or 2, you also have to set the **path** where the file will be saved, and the **output_format**. Check the :doc:`mono_to_stereo() <../pyNAVIS.functions>` function for more information.


To plot the output file, you can use the graphs presented in :doc:`previous examples <Load file>`.

.. prompt:: python \

    from pyNAVIS import *
    settings = MainSettings(num_channels=16, mono_stereo=0, on_off_both=1, address_size=2, ts_tick=0.2, bin_size=10000)
    mono_file = Loaders.loadAEDAT('path/to/file/name.aedat', settings)
    stereo_file = Functions.mono_to_stereo(mono_file, delay=0, settings=settings, return_save_both=0)
    settings.mono_stereo = 1
    Plots.spikegram(stereo_file, settings)

.. warning::
    To plot the information, **mono_stereo** has to be set to 1.



From stereo to mono
###################

The same procedure, but using the ``stereo_to_mono()``, can be applied to convert a stereo file to a mono file:

.. prompt:: python \

    from pyNAVIS import *
    settings = MainSettings(num_channels=16, mono_stereo=1, on_off_both=1, address_size=2, ts_tick=0.2, bin_size=10000)
    stereo_file = Loaders.loadAEDAT('path/to/file/name.aedat', settings)
    mono_file = Functions.stereo_to_mono(stereo_file, left_right=0, settings=settings, return_save_both=0)
    settings.mono_stereo = 0
    Plots.spikegram(mono_file, settings)

For more information regarding this function, see :doc:`stereo_to_mono() <../pyNAVIS.functions>`.

Three-axis files
################

Besides mono (**mono_stereo=0**) and stereo (**mono_stereo=1**) files, pyNAVIS supports three-axis files (**mono_stereo=2**, also available as ``MainSettings.THREE_AXIS``).
The addresses of each axis are stored in consecutive blocks of ``num_channels*(on_off_both+1)`` addresses: first the X axis, then the Y axis and finally the Z axis.

.. prompt:: python \

    from pyNAVIS import *
    settings = MainSettings(num_channels=43, mono_stereo=2, on_off_both=1, address_size=4, ts_tick=1, bin_size=20000)
    three_axis_file = Loaders.loadAEDAT('examples/test_files/NTAS_Accel_3Axxis_43ch_ONOFF_addr4b_ts1.aedat', settings)
    Plots.spikegram(three_axis_file, settings)
    x, y, z, avg_fig = Plots.average_activity(three_axis_file, settings)

.. note::
    The example file is a 1 s accelerometer recording made with the jAER chip ``NUS_3Axxis_42ch``. Each axis has 42 active
    channels placed in a 43-channel slot (the last channel of each slot is always empty), so **num_channels** is set to 43.
    jAER saves 4-byte addresses with a 1 us tick, so **address_size=4** and **ts_tick=1**.

A single axis can be extracted as a mono file with ``stereo_to_mono()``, setting **left_right** to 0, 1 or 2 for the X, Y or Z axis:

.. prompt:: python \

    z_file = Functions.stereo_to_mono(three_axis_file, left_right=2, settings=settings, return_save_both=0)

.. note::
    ``Plots.difference_between_LR()`` is only available for stereo files.
