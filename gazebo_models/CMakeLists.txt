cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

if(COMMAND CMAKE_POLICY)
  CMAKE_POLICY(SET CMP0003 NEW)
  CMAKE_POLICY(SET CMP0004 NEW)
endif(COMMAND CMAKE_POLICY)

set (CMAKE_INSTALL_PREFIX /usr/share CACHE STRING "Install path prefix")

project (gazebo_models)
string (TOLOWER ${PROJECT_NAME} PROJECT_NAME_LOWER)

exec_program(date ARGS -u +%Y-%m-%-dT%H:%M:%S OUTPUT_VARIABLE CURRENT_DATE)

set (dirs
asphalt_plane
ball_bearing
beer
bookshelf
bowl
breakable_test
brick_box_3x1x3
cabinet
camera
cart_front_steer
cart_rigid_suspension
cart_soft_suspension
cinder_block
cinder_block_2
cinder_block_wide
coke_can
construction_barrel
construction_cone
cordless_drill
create
cricket_ball
cube_20k
door_handle
double_pendulum_with_base
drc_practice_2x4
drc_practice_2x6
drc_practice_4x4x20
drc_practice_4x4x40
drc_practice_angled_barrier_135
drc_practice_angled_barrier_45
drc_practice_ball_valve
drc_practice_ball_valve_wall
drc_practice_base_4x8
drc_practice_block_wall
drc_practice_blue_cylinder
drc_practice_door_4x8
drc_practice_handle_wheel_valve
drc_practice_handle_wheel_valve_wall
drc_practice_hand_wheel_valve
drc_practice_hand_wheel_valve_wall
drc_practice_hinged_door
drc_practice_ladder
drc_practice_orange_jersey_barrier
drc_practice_truss
drc_practice_valve_wall
drc_practice_weighted_door
drc_practice_wheel_valve
drc_practice_wheel_valve_large
drc_practice_wheel_valve_large_wall
drc_practice_wheel_valve_wall
drc_practice_white_jersey_barrier
drc_practice_wood_slats
drc_practice_yellow_parking_block
dumpster
fast_food
fire_hose_long
fire_hose_long_curled
fire_hydrant
gas_station
grey_wall
ground_plane
hammer
hinged_door
hokuyo
house_1
house_2
house_3
husky
irobot_hand
iss
iss_half
jersey_barrier
kinect
kitchen_dining
ladder_60deg_0rails
ladder_60deg_1rails
ladder_60deg_2rails
ladder_75deg_0rails
ladder_75deg_1rails
ladder_75deg_2rails
lamp_post
mailbox
marble_1_5cm
metal_peg
metal_peg_board
monkey_wrench
mud_box
nist_elevated_floor_120
nist_fiducial_barrel
nist_maze_wall_120
nist_maze_wall_240
nist_maze_wall_triple_holes_120
nist_simple_ramp_120
nist_stairs_120
office_building
pioneer2dx
pioneer3at
plastic_cup
polaris_ranger_ev
polaris_ranger_xp900
polaris_ranger_xp900_no_roll_cage
powerplant
pr2
pr2_gripper
quadrotor
ragdoll
robocup09_spl_field
robocup14_spl_field
robocup14_spl_goal
robocup_3Dsim_ball
robocup_3Dsim_field
robocup_3Dsim_goal
robocup_spl_ball
robonaut
round_tin_base
round_tin_top
saucepan
simple_arm
simple_arm_gripper
simple_gripper
speed_limit_sign
standpipe
starting_pen
stereo_camera
stone_10_2_5_1cm
stop_sign
sun
table
table_marble
tube_2_25cm
tube_9_5mm
turtlebot
utility_cart
washer
willowgarage
wood_block_10_2_1cm
wood_cube_10cm
wood_cube_2_5cm
wood_cube_5cm
wood_cube_7_5cm
wooden_board
wooden_case
wooden_case_metal_peg
wooden_case_wooden_peg
wooden_peg
wooden_peg_board
youbot
)

set (MODEL_URI_LIST "" CACHE INTERNAL "Model URI List" FORCE)

# Convert install prefix to an absolute path, to support the common case of
# doing this (which otherwise will fail during the install):`
#   cmake -DCMAKE_INSTALL_PREFIX=~/.gazebo ..
get_filename_component(CMAKE_INSTALL_PREFIX_ABSOLUTE ${CMAKE_INSTALL_PREFIX}
  ABSOLUTE)

foreach (dir ${dirs})
  add_custom_target(${dir} ALL COMMAND mkdir -p ${PROJECT_BINARY_DIR}/${dir}
    COMMAND tar czvf ${PROJECT_BINARY_DIR}/${dir}/model.tar.gz ../${dir})

  install (DIRECTORY ${dir} DESTINATION ${CMAKE_INSTALL_PREFIX_ABSOLUTE}/models)
  install (FILES ${PROJECT_BINARY_DIR}/${dir}/model.tar.gz DESTINATION
    ${CMAKE_INSTALL_PREFIX_ABSOLUTE}/models/${dir})
  set(MODEL_URI_LIST "${MODEL_URI_LIST}<uri>file://${dir}</uri>\n")
endforeach ()

configure_file("${CMAKE_SOURCE_DIR}/manifest.xml.in"
               "${PROJECT_BINARY_DIR}/manifest.xml")

install (FILES "${PROJECT_BINARY_DIR}/manifest.xml" DESTINATION
         ${CMAKE_INSTALL_PREFIX_ABSOLUTE}/models/)

configure_file("${CMAKE_SOURCE_DIR}/database.config.in"
               "${PROJECT_BINARY_DIR}/database.config")

install (FILES "${PROJECT_BINARY_DIR}/database.config" DESTINATION
         ${CMAKE_INSTALL_PREFIX_ABSOLUTE}/models/)

message (STATUS "Install path: ${CMAKE_INSTALL_PREFIX_ABSOLUTE}/models")

# This must always be last!
include(CPack)
