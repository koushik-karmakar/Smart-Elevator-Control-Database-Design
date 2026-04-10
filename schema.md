## Eraser Diagram Code
```
organizations [icon: briefcase, color: blue] {
  id serial pk
  name varchar(50)
  slug varchar(100)
  plan_type enum(starter,professional,enterprise)
  max_buildings int
  max_elevators int
  billing_email varchar(150)
  is_active boolean
  trial_ends_at timestamp
  created_at timestamp
  updated_at timestamp
}

users [icon: user, color: blue] {
  id serial pk
  organization_id int fk
  email varchar(150)
  full_name varchar(50)
  phone varchar(20)
  role enum(super_admin,org_admin,building_manager,technician,viewer,api_user)
  password_hash varchar(255)
  totp_secret varchar(100)
  totp_enabled boolean
  is_active boolean
  email_verified_at timestamp
  last_login_at timestamp
  created_at timestamp
  updated_at timestamp
}

buildings [icon: home, color: blue] {
  id serial pk
  organization_id int fk
  name varchar(50)
  slug varchar(100)
  address_line1 varchar(255)
  address_line2 varchar(255)
  city varchar(20)
  state varchar(20)
  country varchar(20)
  postal_code varchar(10)
  latitude decimal(10,2)
  longitude decimal(10,2)
  building_type enum(commercial,residential,hospital,airport,mall,mixed_use)
  total_floors int
  total_basements int
  timezone varchar(60)
  contact_name varchar(50)
  contact_email varchar(150)
  contact_phone varchar(20)
  is_active boolean
  onboarded_at timestamp
  created_at timestamp
  updated_at timestamp
}

floors [icon: layers, color: blue] {
  id serial pk
  building_id int fk
  floor_number int
  floor_label varchar(50)
  floor_type enum(basement,ground,standard,mechanical,rooftop)
  is_accessible boolean
  is_restricted boolean
  is_lobby boolean
  is_parking boolean
  max_occupancy int
  created_at timestamp
  updated_at timestamp
}

elevator_shafts [icon: box, color: blue] {
  id serial pk
  building_id int fk
  shaft_code varchar(20)
  zone_label varchar(50)
  min_floor int
  max_floor int
  shaft_type enum(passenger,freight,service,panoramic)
  notes text
  created_at timestamp
  updated_at timestamp
}

elevators [icon: zap, color: blue] {
  id serial pk
  shaft_id int fk
  building_id int
  elevator_code varchar(30)
  display_name varchar(100)
  model varchar(100)
  manufacturer varchar(100)
  serial_number varchar(100)
  capacity_persons int
  capacity_kg decimal(10,2)
  speed_mps decimal(10,2)
  drive_type enum(traction,hydraulic,mrl,pneumatic)
  door_type enum(single_slide,double_slide,center_open)
  has_camera boolean
  has_emergency_phone boolean
  has_backup_power boolean
  warranty_expiry date
  last_inspection_date date
  next_inspection_due date
  is_active boolean
  created_at timestamp
  updated_at timestamp
}

elevator_floor_coverage [icon: git-merge, color: blue] {
  id serial pk
  elevator_id int fk
  floor_id int fk 
  is_express_stop boolean
  stop_order int
  access_type enum(standard, restricted, emergency_only)
  created_at timestamp
}

elevator_configurations [icon: settings, color: blue] {
  id serial pk
  elevator_id int fk 
  door_open_time_sec int
  max_idle_sec int
  load_threshold_percent int
  overload_alert_kg decimal(10,2)
  acceleration_mps2 decimal(10,2)
  preferred_home_floor int
  dispatch_mode enum(auto,manual,eco,express)
  effective_from timestamp
  effective_to timestamp
  created_by int
  created_at timestamp
}

building_user_access [icon: shield, color: blue] {
  id serial pk
  user_id int fk 
  building_id int fk
  access enum(full,read_only,maintenance_only,reports_only)
  granted_by int fk
  valid_from timestamp
  valid_to timestamp
  created_at timestamp
}

api_keys [icon: key, color: blue] {
  id serial pk
  organization_id int fk 
  created_by int kf
  name varchar(100)
  key_hash varchar(255)
  key_prefix varchar(10)
  scopes varchar(500)
  rate_limit_per_min int
  is_active boolean
  last_used_at timestamp
  expires_at timestamp
  created_at timestamp
}

elevator_status [icon: activity, color: purple] {
  id serial pk
  elevator_id int fk 
  status enum(idle,moving_up,moving_down,door_open, door_closed,maintenance,offline,emergency,overloaded)
  current_floor int
  target_floor int
  direction enum(up, down, stationary)
  load_kg decimal(10,2)
  load_percent int
  door_status enum(open,closed,obstructed,fault)
  is_overloaded boolean
  is_emergency_active boolean
  last_heartbeat_at timestamp
  status_changed_at timestamp
  updated_at timestamp
}

elevator_sensor_readings [icon: radio, color: purple] {
  id serial pk
  elevator_id int fk
  reading_type enum(temperature,vibration,door_force,load_cell,speed,voltage)
  value decimal(10,2)
  unit varchar(20)
  anomaly_threshold decimal
  recorded_at timestamp
}

elevator_fault_events [icon: alert-triangle, color: purple] {
  id serial pk
  elevator_id int
  fault_code varchar(30)
  fault_category enum(mechanical, electrical, software, sensor, door, communication)
  severity enum(info, warning, critical, fatal)
  description text
  is_resolved boolean
  resolved_at timestamp
  auto_detected boolean
  triggered_at timestamp
  created_at timestamp
}

floor_requests [icon: arrow-up-circle, color: green] {
  id serial pk
  building_id int fk 
  floor_id int fk
  direction_requested enum(up,down)
  request_source enum(physical_button,mobile_app,kiosk,api,accessibility_device)
  request_status enum(pending, assigned, in_progress, fulfilled, cancelled, expired)
  passenger_count_estimated int
  accessibility_required boolean
  priority enum(normal, high, emergency)
  requested_at timestamp
  assigned_at timestamp
  fulfilled_at timestamp
  cancelled_at timestamp
  expires_at timestamp
}

destination_requests [icon: map-pin, color: green] {
  id serial pk
  floor_request_id int fk
  destination_floor_id int fk 
  passenger_count int
  accessibility_required boolean
  created_at timestamp
}

ride_assignments [icon: check-circle, color: green] {
  id serial pk
  floor_request_id int fk 
  elevator_id int fk
  assignment_status enum(queued,accepted,in_progress,completed,failed,cancelled)
  algorithm_used enum(nearest,round_robin,zone_based,ml_optimized)
  estimated_wait_sec int
  actual_wait_sec int
  assignment_score decimal
  retry_count int
  assigned_at timestamp
  completed_at timestamp
  cancelled_at timestamp
  cancellation_reason varchar(255)
  created_at timestamp
}

ride_logs [icon: book-open, color: orange] {
  id serial pk
  elevator_id int fk
  ride_assignment_id int fk
  building_id int fk
  fault_event_id int
  origin_floor int
  destination_floor int
  stops_made int
  passenger_count int
  load_kg decimal(10,2)
  load_percent int
  duration_sec int
  wait_time_sec int
  distance_floors int
  energy_kwh decimal(10,2)
  door_cycles int
  was_overloaded boolean
  had_fault boolean
  started_at timestamp
  ended_at timestamp
  created_at timestamp
}

daily_elevator_stats [icon: bar-chart-2, color: orange] {
  id serial pk
  elevator_id int fk 
  building_id int fk
  stat_date date
  total_rides int
  total_passengers int
  total_distance_floors int
  total_runtime_sec int
  total_idle_sec int
  total_energy_kwh decimal
  total_door_cycles int
  peak_load_kg decimal
  avg_wait_sec int
  avg_duration_sec int
  fault_count int
  uptime_percent decimal
  created_at timestamp
  updated_at timestamp
}

floor_traffic_stats [icon: trending-up, color: orange] {
  id serial pk
  floor_id int fk
  building_id int fk
  stat_date date
  stat_hour int
  total_up_requests int
  total_down_requests int
  total_fulfilled int
  total_cancelled int
  avg_wait_sec int
  peak_demand_count int
  created_at timestamp
}

maintenance_schedules [icon: tool, color: red] {
  id serial pk
  elevator_id int fk 
  maintenance_type enum(routine,preventive,corrective,emergency,inspection,certification)
  title varchar(200)
  description text
  priority enum(low,medium,high,critical)
  status enum(scheduled,in_progress, completed,skipped,overdue,cancelled)
  assigned_company varchar(150)
  assigned_technician varchar(150)
  technician_phone varchar(30)
  estimated_duration_min int
  scheduled_at timestamp
  started_at timestamp
  completed_at timestamp
  cancelled_at timestamp
  next_due_at timestamp
  recurrence_interval_days int
  parts_budget decimal
  created_by int fk
  created_at timestamp
  updated_at timestamp
}

maintenance_logs [icon: clipboard, color: red] {
  id serial pk
  elevator_id int
  maintenance_schedule_id int
  fault_event_id int
  action_taken text
  root_cause varchar(500)
  parts_replaced text
  parts_cost decimal
  labour_cost decimal
  outcome enum(resolved, partially_resolved, escalated, deferred, no_action_required)
  elevator_downtime_min int
  performed_by varchar(150)
  verified_by varchar(150)
  remarks text
  attachments_urls text
  performed_at timestamp
  created_at timestamp
}

maintenance_parts [icon: package, color: red] {
  id serial pk
  maintenance_log_id int fk 
  part_name varchar(150)
  part_number varchar(100)
  manufacturer varchar(100)
  quantity int
  unit_cost decimal
  total_cost decimal
  supplier varchar(150)
  warranty_months int
  installed_at timestamp
  created_at timestamp
}

audit_logs [icon: file-text, color: teal] {
  id serial pk
  user_id int fk 
  organization_id int fk 
  building_id int fk 
  elevator_id int fk
  action varchar(100)
  entity_type varchar(60)
  entity_id int
  old_value varchar(100)
  new_value varchar(100)
  ip_address varchar(45)
  user_agent varchar(100)
  created_at timestamp
}

notifications [icon: bell, color: teal] {
  id serial pk
  organization_id int fk 
  building_id int fk 
  elevator_id int fk 
  triggered_by_user_id int fk 
  channel enum(email,sms,push,webhook,slack)
  type enum(fault_alert,maintenance_due,overload,offline,daily_report,certification_expiry)
  severity enum(info,warning,critical)
  title varchar(100)
  body varchar(200)
  payload varchar(200)
  is_read boolean
  is_sent boolean
  sent_at timestamp
  created_at timestamp
}

users.organization_id > organizations.id
building_user_access.user_id > users.id
building_user_access.building_id > buildings.id
building_user_access.granted_by > users.id
api_keys.organization_id > organizations.id
api_keys.created_by > users.id

buildings.organization_id > organizations.id
floors.building_id > buildings.id
elevator_shafts.building_id > buildings.id
elevators.building_id > buildings.id
elevators.shaft_id > elevator_shafts.id

elevator_floor_coverage.elevator_id > elevators.id
elevator_floor_coverage.floor_id > floors.id
elevator_configurations.elevator_id > elevators.id
elevator_configurations.created_by > users.id

elevator_status.elevator_id - elevators.id
elevator_sensor_readings.elevator_id > elevators.id
elevator_fault_events.elevator_id > elevators.id

floor_requests.building_id > buildings.id
floor_requests.floor_id > floors.id
destination_requests.floor_request_id > floor_requests.id
destination_requests.destination_floor_id > floors.id
ride_assignments.floor_request_id > floor_requests.id
ride_assignments.elevator_id > elevators.id

ride_logs.elevator_id > elevators.id
ride_logs.ride_assignment_id > ride_assignments.id
ride_logs.building_id > buildings.id
ride_logs.fault_event_id > elevator_fault_events.id
daily_elevator_stats.elevator_id > elevators.id
daily_elevator_stats.building_id > buildings.id
floor_traffic_stats.floor_id > floors.id
floor_traffic_stats.building_id > buildings.id

maintenance_schedules.elevator_id > elevators.id
maintenance_schedules.created_by > users.id
maintenance_logs.elevator_id > elevators.id
maintenance_logs.maintenance_schedule_id > maintenance_schedules.id
maintenance_logs.fault_event_id > elevator_fault_events.id
maintenance_parts.maintenance_log_id > maintenance_logs.id

audit_logs.user_id > users.id
audit_logs.organization_id > organizations.id
audit_logs.building_id > buildings.id
audit_logs.elevator_id > elevators.id
notifications.organization_id > organizations.id
notifications.building_id > buildings.id
notifications.elevator_id > elevators.id
notifications.triggered_by_user_id > users.id
```
