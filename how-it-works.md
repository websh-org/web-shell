# WebShell: How It Works

````js

shell
  bios
    factory
  remote-controller
    + bios

  app-controller
    + bios
    + remote-controller
  fs
    + bios

  fs-adapter
    + bios
    + remote-controller

shell {
  app-controller[] {
    iframe
    master-port
    app-api[] {
      *master-port
    }
  }
  fs-adapter-controller[] {
    iframe
    master-port
    fs-api[] {
      *master-port
    }
  }
  fs {
    volume[] {
      *fs-adapter-controller
    }
  }
}


shell {
  app-manager {
    app-controller[] {
      iframe
      app-port
      app-api[] {
        api-port
      }
    }
  }
  registry-manager {
    registry-controller {
      iframe
      registry-port
      app-api[] {
        api-port
      }
    }
  }
  fs-manager {
    fs-adapter-controller[] {
      iframe
      fs-adapter-port
      fs-api[] {
        api-port
      }
    }
  }
  fs {
    volume[] {
      fs-manager-port
    }
  }
}

desktop {
  file-manager {
    fs-port
  }
  app-window[] {
    app-port
    fm-port
  }
  fs-adapter-window[] {
    fs-adapter-port
  }
}

````