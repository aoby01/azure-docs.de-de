# Übersicht
## [Was ist Site Recovery?](site-recovery-overview.md)
## Wie funktioniert Site Recovery?
### [Azure-zu-Azure-Architektur](site-recovery-azure-to-azure-architecture.md)
### [Vmware-zu-Azure-Architektur](site-recovery-architecture-vmware-to-azure.md)
### [Hyper-V-zu-Azure-Architektur](site-recovery-architecture-hyper-v-to-azure.md)
### [Architektur bei Replikation an einem sekundären Standort](site-recovery-architecture-to-secondary-site.md)
## [Welche Workloads können Sie schützen?](site-recovery-workload.md)
## Site Recovery-Supportmatrix
### [Azure zu Azure – Support](site-recovery-support-matrix-azure-to-azure.md)
### [Lokal zu Azure – Support](site-recovery-support-matrix-to-azure.md)
### [Lokal zu sekundärem Standort – Support](site-recovery-support-matrix-to-sec-site.md)
## [Häufig gestellte Fragen](site-recovery-faq.md)
## [Ansehen einer Einführung](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Erste Schritte
## [Replikation von Azure-VMs (Vorschau)](site-recovery-azure-to-azure.md)
## [Replizieren physischer Server in Azure](site-recovery-physical-servers-to-azure.md)
## [Replizieren von Hyper-V-VMs in Azure (mit VMM)](site-recovery-vmm-to-azure.md)
## [Replizieren von Hyper-V-VMs in Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replizieren von Hyper-V-VMs an einem sekundären Standort (mit VMM)](site-recovery-vmm-to-vmm.md)
## [Replizieren von VMware-VMs und physischen Servern an einem sekundären Standort](site-recovery-vmware-to-vmware.md)
## [Replizieren von VMware-VMs in Azure in einer mehrinstanzenfähigen Bereitstellung (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Anleitung
## Planen
### [Voraussetzungen für die Azure-Replikation](site-recovery-azure-to-azure-prereq.md)
### Planen von Netzwerken
#### [Planen von Netzwerken für die Replikation von Azure zu Azure (Vorschau)](site-recovery-azure-to-azure-networking-guidance.md)
#### [Planen von Netzwerken für die Replikation lokaler Computer](site-recovery-network-design.md)
#### [Planen von Netzwerken für die Replikation virtueller Azure-Computer](site-recovery-network-mapping-azure-to-azure.md)
#### [Planen der Netzwerkzuordnung für die Replikation virtueller Hyper-V-Computer](site-recovery-network-mapping.md)
### Planen der Kapazität und Skalierbarkeit
#### [Planen der Kapazität der VMware-Replikation in Azure](site-recovery-plan-capacity-vmware.md)
#### [Deployment Planner für die VMware-Replikation in Azure](site-recovery-deployment-planner.md)
#### [Capacity Planner für die Hyper-V-Replikation](site-recovery-capacity-planner.md)
### [Planen des rollenbasierten Zugriffs für die VM-Replikation](site-recovery-role-based-linked-access-control.md)
## Bereitstellen
### [Replizieren von VMware-VMs in Azure](vmware-walkthrough-overview.md)
#### [Schritt 1: Überprüfen der Architektur](vmware-walkthrough-architecture.md)
#### [Schritt 2: Überprüfen der Voraussetzungen und Einschränkungen](vmware-walkthrough-prerequisites.md)
#### [Schritt 3: Planen der Kapazität](vmware-walkthrough-capacity.md)
#### [Schritt 4: Planen von Netzwerken](vmware-walkthrough-network.md)
#### [Schritt 5: Vorbereiten von Azure](vmware-walkthrough-prepare-azure.md)
#### [Schritt 6: Vorbereiten von VMware](vmware-walkthrough-prepare-vmware.md)
#### [Schritt 7: Erstellen eines Tresors](vmware-walkthrough-create-vault.md)
#### [Schritt 8: Quelle und Ziel austauschen](vmware-walkthrough-source-target.md)
#### [Schritt 9: Einrichten einer Replikationsrichtlinie](vmware-walkthrough-replication.md)
#### [Schritt 10: Installieren des Mobilitätsdiensts](vmware-walkthrough-install-mobility.md)
#### [Schritt 11: Aktivieren der Replikation](vmware-walkthrough-enable-replication.md)
#### [Schritt 12: Ausführen eines Testfailovers](vmware-walkthrough-test-failover.md)
## Konfigurieren
### Einrichten der Quellumgebung
#### [Quellumgebung – VMware zu Azure](site-recovery-set-up-vmware-to-azure.md)
#### [Quellumgebung – physisch zu Azure](site-recovery-set-up-physical-to-azure.md)
### Einrichten der Zielumgebung
#### [Zielumgebung – VMware zu Azure](site-recovery-prepare-target-vmware-to-azure.md)
#### [Zielumgebung – physisch zu Azure](site-recovery-prepare-target-physical-to-azure.md)
### [Konfigurieren der Replikationseinstellungen](site-recovery-setup-replication-settings-vmware.md)
### [Bereitstellen des Mobilitätsdiensts für die VMware-Replikation](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Bereitstellen des Mobilitätsdiensts mithilfe von System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Bereitstellen des Mobilitätsdiensts mithilfe von Azure Automation DSC](site-recovery-automate-mobility-service-install.md)
### Replikation aktivieren
#### [Ermöglichen der Replikation von Azure zu Azure](site-recovery-replicate-azure-to-azure.md)
#### [Ermöglichen der Replikation von VMware zu Azure](site-recovery-replicate-vmware-to-azure.md)
## Failover und Failback
### [Einrichten von Wiederherstellungsplänen](site-recovery-create-recovery-plans.md)
#### [Hinzufügen von Azure-Runbooks zu Wiederherstellungsplänen](site-recovery-runbook-automation.md)
### Durchführen eines Test-Failovers
#### [Ausführen eines Testfailovers auf Azure](site-recovery-test-failover-to-azure.md)
#### [Ausführen eines Testfailovers zwischen VMM-Clouds](site-recovery-test-failover-vmm-to-vmm.md)
### [Ausführen eines Failovers für geschützte Computer](site-recovery-failover.md)
### Erneutes Schützen von Computern nach einem Failover
#### [Erneutes Schützen von einer sekundären zu einer primären Azure-Region](site-recovery-how-to-reprotect-azure-to-azure.md)
#### [Erneutes Schützen von Azure zu lokal](site-recovery-how-to-reprotect.md)
### Failback von Azure
#### [Failback von Azure nach VMware](site-recovery-failback-azure-to-vmware.md)
#### [Failback von Azure nach Hyper-V](site-recovery-failback-from-azure-to-hyper-v.md)
## Migrieren
### [Migrieren zu Azure](site-recovery-migrate-to-azure.md)
### [Zwischen Azure-Regionen migrieren](site-recovery-migrate-azure-to-azure.md)
### [Migrieren von AWS Windows-Instanzen zu Azure](site-recovery-migrate-aws-to-azure.md)
### [Replizieren migrierter Computer in einer anderen Azure-Region](site-recovery-azure-to-azure-after-migration.md)
## Workloads
### [Active Directory und DNS](site-recovery-active-directory.md)
### [Replizieren von SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Azure IoT Hub](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [IIS-basierte Webanwendungen](site-recovery-iis.md)
### [Citrix XenApp und XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Andere Workloads](site-recovery-workload.md#workload-summary)
## Automatisieren der Replikation
### [Automatisieren der Hyper-V-Replikation in Azure (ohne VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatisieren der Hyper-V-Replikation in Azure (mit VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatisieren der Hyper-V-Replikation an einem sekundären Standort (mit VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Verwalten
### [Verwalten von Prozessservern in Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Verwalten des Konfigurationsservers](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Verwalten eines horizontal hochskalierten Prozessservers](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Verwalten von vCenter-Servern](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Entfernen von Servern und Deaktivieren des Schutzes](site-recovery-manage-registration-and-protection.md)

## Überwachen und Behandeln von Problemen
### [Probleme bei der Replikation von Azure zu Azure](site-recovery-azure-to-azure-troubleshoot-errors.md)
### [Probleme bei der Replikation zwischen einem lokalen Standort und Azure](site-recovery-vmware-to-azure-protection-troubleshoot.md)
### [Erfassen von Protokollen und Beheben lokaler Probleme](site-recovery-monitoring-and-troubleshooting.md)

# Referenz
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell (klassisch)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Verwandte Themen
## [Azure Automation](/azure/automation/)

# Ressourcen
## [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Preise](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=site-recovery)
