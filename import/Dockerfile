# Use the existing image as the base
FROM autogen_dev_img

# Copy your files into the container - anything in this directory
COPY ./scripts /home/autogen/autogen/import

# This fixes some really weird package/dependency errors that I was facing.
RUN pip install --force-reinstall autogen
RUN pip install --force-reinstall pyautogen

# This package caused so many headaches. There's also some weird sudo pip nonsense happening
RUN sudo pip install --force-reinstall typing-extensions

# This fixed some more of the sudo pip nonsense I was facing
RUN sudo chown autogen:autogen -R /home/autogen/

# Command to run when the container starts
CMD ["/bin/bash"]